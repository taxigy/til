# Quick start with Elm

Quick start matters. With quick start, you can see that something works _before_ you have learned how to properly code it. In this example, a slightly modified example from [official docs](https://guide.elm-lang.org/architecture/) is used.

First, install:

```bash
brew update; brew install elm
elm package install "elm-lang/html"
```

Install [elm-format](https://github.com/avh4/elm-format). There's no easy way, you need to download a tgz, unpack it and put somewhere, say, into ~/.elm-format.

Add elm-format package to Atom, if you use Atom. The easiest way to get started with Elm is to use Atom.

```bash
apm install elm-format
```

And add the first source file to the project:

```bash
touch Main.elm
atom .
```

In Main.elm,

- declare the module: this one will be Main, and
- import Html module that has just been installed:

```elm
module Main exposing (..)

import Html exposing (..)
import Html exposing (beginnerProgram)
import Html.Events exposing (..)


model =
    { showFace = False }


type Msg
    = ShowFace


update msg model_ =
    case msg of
        ShowFace ->
            { model_ | showFace = True }


view model_ =
    div []
        [ h1 [] [ text "Face Generator" ]
        , button
            [ onClick ShowFace
            ]
            [ text "show face" ]
        , if model_.showFace then
            text "you see face"
          else
            text "you don't see face"
        ]


main =
    beginnerProgram
        { model = model
        , update = update
        , view = view
        }
```

Save it.

To see if it works, run elm-reactor (all-in-one watcher, builder and dev server):

```bash
elm-reactor
```

By default, the server will run on port 8000. Go to http://localhost:8000.

Done.

----

Useful links:

- [The Elm Architecture](https://guide.elm-lang.org/architecture/).
- [elm-format](https://github.com/avh4/elm-format).
- [elm-oracle](https://github.com/ElmCast/elm-oracle)
- [Elm's notorious human-friendly error message, screencast](http://ajhager.com/elm_compiler_errors_and_vim/).
