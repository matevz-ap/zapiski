# Android UI
Usually provided via Activities and Fragments
- setContentView(View v)

## View
- main class on which the View System operates
- responsible for drawing itself and handling events
- can be instantiated through:
    - XML
    - source code
- views are organized in a tree with the root view containing all the others
- displaying views includes:
    - measuring each of the elements (onMeasure())
    - aligning children (onLayout())
    - rendering the view (onDraw())

## Fragments
- support more dynamic and flexible UI designs, element reusability
- unlike Activity, a Fragment:
    - needs a parent Activity
    - need not be the only Fragment on the screen
    - does not talk directly to other Fragments

### Communication
- fragment can access the underlying Context
    - getContext() or getActivity()
- activity can access fragments via FragmentManager
    - findFragmentByID() or findFragmentByTag()
- listeners -> a method is called when the monitored data is modified
