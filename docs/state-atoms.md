# State Atoms

## Overview

Each state atom represents a specific and isolated domain of application state. State atoms are always imported at the directory level should not be imported as an individual file with its directory. As such, the public API of a state atom should consist of only the things exported at root directory's `index.js`.

## Anatomy of a State Atom

State atoms will always export the following:

* the semantic actions that the state atom owns (Actions)
* functions to create descriptions of these actions (ActionCreators)
* state mutations (Reducer)
* state transformations (selectors)

## Relational State

It is not uncommon to have state atoms contain relational data referenced in other state atoms. Each state atom should be the source of truth for its state and, therefore, should not contain other atoms' state within its graph. Relational data should be "linked" by the ID of the related state. So, for example:

```javascript
// Project State Atom
{
    projects: [
        {
            id: 'Project:1',
            tasks: [
                'Task:1',
                'Task:2'
            ]
        }
    ]
}

// Task State Atom
{
    tasks: [
        {
            id: 'Task:1',
            project: 'Project:1'
        },
        {
            id: 'Task:2',
            project: 'Project:1'
        }
    ]
}
```

### Ownership of Bi-Directional Actions

Given the above example, it is ambiguous which state atom actually "owns" the action of updating the relationship between `Project` and `Task`. It is unclear whether a 'Project' **own** a 'Task' or does a 'Task' get **assigned** to a 'Project'? Both state atoms can be reasoned to equally own this responsibility. When asking the "who owns this action" question, the simple answer is that neither one owns the action. State atoms only own actions directly related to their domain. Bi-directional actions are not singularly related to their underlying, affected state. Instead, these actions are owned by a higher-level state atom; one which is composed of other state atoms. This is to say that state atoms may consume action creators of other state atoms and dispatch these actions within their own action creators.

This upholds state atom autonomy and enables state atoms to be composed in arbitrary ways to collectively create a [feature](/feature.md).

#### An Example

Given the `Project` and `Task` state atoms above, the assignment of a `Task` to a `Project` would live in a third state atom; `Assignment` or possibly `TaskAssignment`. This third state atom would contain an action creator `AssignTask` that would dispatch actions from both `Project` and `Task` to facilitate the bi-directional updates. Relevant features needing to assign tasks to projects would consume all three of the state atoms.

```javascript
// Assignment state atom's AssignTask action creator
import * as TaskAtom from './../../Task';
import * as ProjectAtom from './../../Project';

const assignTask = (taskId, projectId) => (dispatch) => {
    dispatch(TaskAtom.ActionCreators.updateTask({
        id: taskId,
        project: projectId
    });
    dispatch(ProjectAtom.ActionCreators.updateProject({
        id: projectId,
        tasks: [taskId]
    });
};

```

#### Using a State Atom without All of its Dependencies

In the above example, what happens when the feature includes the `Assignment` and `Task` state atoms, but forgets to consume the `Project` state atom? The `Assignment` state atom "depends" on both `Project` and `Task` atoms. Even when removing a dependency of a state atom, the application will still function as would be expected. Why is this? Simply put, when assigning a task to a project, an action is dispatched for updating `Project` that simply is ignored; as there are no listeners to this action. The `Task` state atom will still be updated and the missing `Project` state atom never existed to begin with.

However, in most cases, even this is an unlikely scenario. Colloquially, assigning a task to a project, implies that both task **and** project are present. If project was not present, then how would you know the project for the task to be assigned to in the first place? The identification of missing state atoms should be apparent both in the vernacular of describing the problem, as well as missing data before reaching the bi-directional action.  