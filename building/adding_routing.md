# Adding routing

Let's add a router for our application. We will be using [Hop](https://github.com/sporto/hop) version 2.1.

## Routing

Create a module for defining the application routing configuration. In __src/Routing.elm__:

```elm
module Routing (..) where

import Effects exposing (Effects, Never)
import Hop


{-
Routing Actions

HopAction : is called after Hop has changed the location, we usually don't care about this action
ShowPlayers : Action that instructs to show the players page
EditPlayer : Action to show the Edit player page
ShowNotFound : Action that triggers when the browser location doesn't match any of our routes
NavigateTo : Action to change the browser location
-}


type Action
  = HopAction Hop.Action
  | ShowPlayers Hop.Payload
  | EditPlayer Hop.Payload
  | ShowNotFound Hop.Payload
  | NavigateTo String
  | NoOp



{-
Available views in our application
NotFoundView is necessary when no route matches the location
-}


type AvailableViews
  = PlayersView
  | EditPlayerView
  | NotFoundView



{-
We need to store the payload given by Hop
And we store the current view
-}


type alias Model =
  { routerPayload : Hop.Payload
  , view : AvailableViews
  }


initialModel : Model
initialModel =
  { routerPayload = router.payload
  , view = PlayersView
  }


update : Action -> Model -> ( Model, Effects Action )
update action model =
  case action of
    {-
    NavigateTo
    Called from our application views e.g. by clicking on a button
    asks Hop to change the page location
    -}
    NavigateTo path ->
      ( model, Effects.map HopAction (Hop.navigateTo path) )

    {-
    ShowPlayers and EditPlayer
    Actions called after a location change happens
    these are triggered by Hop
    -}
    ShowPlayers payload ->
      ( { model | view = PlayersView, routerPayload = payload }, Effects.none )

    EditPlayer payload ->
      ( { model | view = EditPlayerView, routerPayload = payload }, Effects.none )

    _ ->
      ( model, Effects.none )



{-
Routes in our application
Each route maps to a view
-}


routes : List ( String, Hop.Payload -> Action )
routes =
  [ ( "/", ShowPlayers )
  , ( "/players", ShowPlayers )
  , ( "/players/:id/edit", EditPlayer )
  ]



{-
Create a Hop router
Hop expects the a list of routes and an action for a location doesn't match
-}


router : Hop.Router Action
router =
  Hop.new
    { routes = routes
    , notFoundAction = ShowNotFound
    }


```

The code should be clear from the comments. In this module we:

- define actions that can happen when a location changes, see `Action`
- define available views, see `AvailableViews`
- define how to react to the actions, see `update`
- define how browser locations map to actions, see `routes`.

## Main Model

The main application model needs to be change to include the routing model. Change __src/Model.elm__ to:

```elm
module Models (..) where

import Players.Models exposing (Player)
import Routing


type alias AppModel =
  { players : List Player
  , routing : Routing.Model
  }


initialModel : AppModel
initialModel =
  { players = [ Player 1 "Sam" 1 ]
  , routing = Routing.initialModel
  }
```

## Main Update

## Main View

## Edit view