---
title: MaterialBanner managed by the ScaffoldMessenger
description: MaterialBanners are now managed by the ScaffoldMessenger.
---

## Summary

`MaterialBanner`s are now handled by `Scaffold`s through the `ScaffoldMessenger` API.

## Context

Prior to this change, `MaterialBanner`s would be shown by placing
them directly within the widget tree.
When a `MaterialBanner` was placed directly in the widget tree,
we imposed a couple restrictions on it.
To name a few, the `MaterialBanner` did not persist
across routes, they had to be placed in the widget tree beforehand
and could not be shown dynamically in response to an event,
and there was no queue to manage multiple `MaterialBanner`s.

The `ScaffoldMessenger` now handles `MaterialBanner`s in order to
combat these limitations the `MaterialBanner` had.
By default, a root `ScaffoldMessenger` is included in the `MaterialApp`,
but you can create your own controlled scope for the `ScaffoldMessenger`
to further control _which_ `Scaffold`s receive your `MaterialBanner`s. 

## Description of change

The previous approach relied upon a boolean to keep track of
whether a `MaterialBanner` should be shown or not as well as a `Column`
to show it above other Widgets.

<!-- skip -->
```dart
Scaffold(
  body: Column(
    children: <Widget>[
      if(shouldShowBanner)
        const MaterialBanner(
         content: Text('Material Banner'),
          actions: <Widget>[
           TextButton(
             child: Text('DISMISS'),
             onPressed: () => setState(() => shouldShowBanner = false),
           ),
          ],
       ),
    ],
  ),
);
```

The new approach calls on the `ScaffoldMessenger` to show
the `MaterialBanner`. In this case, the `shouldShowBanner` is no longer
required to show and hide the MaterialBanner, nor is the `Column` needed
to place it above other widgets.

<!-- skip -->
```dart
Scaffold(
  body: Center(
    child: ElevatedButton(
      child: const Text('Show MaterialBanner'),
      onPressed: () => ScaffoldMessenger.of(context).showMaterialBanner(
        MaterialBanner(
          padding: EdgeInsets.all(20),
          content: Text('Hello, I am a Material Banner'),
          actions: <Widget>[
            TextButton(
              child: Text('DISMISS'),
              onPressed: () {
                ScaffoldMessenger.of(context).hideCurrentMaterialBanner();
               }
            ),
          ],
        ),
      ),
    ),
  ),
);
```

When presenting a `MaterialBanner` during a transition,
the `MaterialBanner` completes a `Hero` animation,
moving smoothly to the next page.

A `Scaffold` can show at most one `MaterialBanner` at a time.
If this function is called while another `MaterialBanner` is already visible,
the given `MaterialBanner` will be added to a queue and displayed after
the earlier `MaterialBanner`s have closed.

To remove the `MaterialBanner` with an exit animation,
use hideCurrentMaterialBanner or call ScaffoldFeatureController.close
on the returned ScaffoldFeatureController.
To remove a `MaterialBanner` suddenly (without an animation),
use removeCurrentMaterialBanner.

## Migration guide

Code before migration:

<!-- skip -->
```dart
// a bool is used to keep track of the MaterialBanner's visibility
bool shouldShowBanner = false;

...

// Generally used in a Column, to place it above other widgets.
Column(
  children: <Widget>[
    // An if statement is used to hide/show the banner
    if(shouldShowBanner)
      const MaterialBanner(
       content: ...,
        actions: <Widget>[
         TextButton(
           child: Text('DISMISS'),
           // Hide the banner by setting the shouldShowBanner to false
           onPressed: () => setState(() => shouldShowBanner = false),
         ),
        ],
     ),
    TextButton(
      child: const Text('Show banner'),
      // Change shouldShowBanner to true to show the banner
      onPressed: () => setState(() => shouldShowBanner = true),
    ),
  ],
);
```

Code after migration:

<!-- skip -->
```dart
ElevatedButton(
  child: const Text('Show MaterialBanner'),
  // ScaffoldMessengerState.showMaterialBanner() is called on
  // to show the MaterialBanner.
  onPressed: () => ScaffoldMessenger.of(context).showMaterialBanner(
    MaterialBanner(
      padding: EdgeInsets.all(20),
      content: Text('Hello, I am a Material Banner'),
      actions: <Widget>[
        TextButton(
          child: Text('DISMISS'),
          onPressed: () {
            // ScaffoldMessengerState.hideCurrentMaterialBanner() is
            // called to remove the material banner with an exit animation
            ScaffoldMessenger.of(context).hideCurrentMaterialBanner();
           }
        ),
      ],
    ),
  )
```

## Timeline

Landed in version: not yet<br>
In stable release: not yet

## References

API documentation:
* [`Scaffold`][]
* [`ScaffoldMessenger`][]
* [`MaterialBanner`][]
* [`MaterialApp`][]

Relevant issues:
* [Issue #60024][]

Relevant PRs:
* [Integrate MaterialBanner with the ScaffoldMessenger API][]

[`Scaffold`]: {{site.api}}/flutter/material/Scaffold-class.html
[`ScaffoldMessenger`]: {{site.api}}/flutter/material/ScaffoldMessenger-class.html
[`MaterialBanner`]: {{site.api}}/flutter/material/MaterialBanner-class.html
[`MaterialApp`]: {{site.api}}/flutter/material/MaterialApp-class.html
[Issue #60024]: {{site.github}}/flutter/flutter/issues/60024
[Integrate MaterialBanner with the ScaffoldMessenger API]: {{site.github}}/flutter/flutter/pull/81706
