# Modular Redux Reference App

This repository represents a reference app for best practices when developing a large, Redux based application. This reference is based on my own experiences and best practices and, as such, will be updated with new best practices as they are found/discovered.

Due to the potential size or prior establishment of the application, this single application is actually a collection of "single page applications." Each individual SPA shares all its pieces and can be more accurately described as a specific composition of the building blocks of the application as a whole.

## Front-end Technology Stack

This reference app is for front-end only and does not concern itself with a particular backend. The core technologies it assumes are:

* [React](https://github.com/facebook/react)
* [Redux](https://github.com/reactjs/redux)
* [Immutablejs](https://github.com/facebook/immutable-js)
* [Reselect](https://github.com/reactjs/reselect)

## Application Architecture Breakdown

The application's architecture is broken down into a few main areas; each of which will be discussed briefly. For more information on any one particular area, please see the relevant file within the [docs](/docs) directory of this repository.

### Components

### State Atoms

Each state atom represents a specific and isolated domain of application state. State atoms are always imported at the directory level should not be imported as an individual file with its directory. As such, the public API of a state atom should consist of only the things exported at root directory's `index.js`. See [state atoms](/docs/state-atoms.md) for more information.
