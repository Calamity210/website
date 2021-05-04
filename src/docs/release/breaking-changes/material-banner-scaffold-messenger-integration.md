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
             onPressed: () => shouldShowBanner = false,
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
              onPressed: () => ScaffoldMessenger.of(context).hideCurrentMaterialBanner(),
            ),
          ],
        ),
      ),
    ),
  ),
);
```

When presenting a `SnackBar` during a transition,
the `SnackBar` completes a `Hero` animation,
moving smoothly to the next page.

The `ScaffoldMessenger` creates a scope in which all descendant
`Scaffold`s register to receive `SnackBar`s,
which is how they persist across these transitions.
When using the root `ScaffoldMessenger` provided by the
`MaterialApp`, all descendant `Scaffold`s receive `SnackBar`s,
unless a new `ScaffoldMessenger` scope is created further down the tree.
By instantiating your own `ScaffoldMessenger`,
you can control which `Scaffold`s receive `SnackBar`s, and which do not based
on the context of your application.

The method `debugCheckHasScaffoldMessenger` is available to assert
that a given context has a `ScaffoldMessenger` ancestor.
Trying to present  a `SnackBar` without a `ScaffoldMessenger` ancestor
present results in an assertion such as the following:

```
No ScaffoldMessenger widget found.
Scaffold widgets require a ScaffoldMessenger widget ancestor.
Typically, the ScaffoldMessenger widget is introduced by the MaterialApp
at the top of your application widget tree.
```


## Migration guide

{% comment %}
  A description of how to make the change.
  If a migration tool is available,
  discuss it here. Even if there is a tool,
  a description of how to make the change manually
  must be provided. This section needs before and
  after code examples that are relevant to the
  developer.
{% endcomment %}

Code before migration:

<!-- skip -->
```dart
// Example of code before the change.
```

Code after migration:

<!-- skip -->
```dart
// Example of code after the change.
```

## Timeline

{% comment %}
  The version # of the SDK where this change was
  introduced.  If there is a deprecation window,
  the version # to which we guarantee to maintain
  the old API. Use the following template:

  If a breaking change has been reverted in a
  subsequent release, move that item to the
  "Reverted" section of the index.md file.
  Also add the "Reverted in version" line,
  shown as optional below. Otherwise, delete
  that line.
{% endcomment %}

Landed in version: xxx<br>
In stable release: not yet
Reverted in version: xxx  (OPTIONAL, delete if not used)

## References

{% comment %}
  These links are commented out because they
  cause the GitHubActions (GHA) linkcheck to fail.
  Remove the comment tags once you fill this in with
  real links. Only use the "master-api" include if
  you link to "master-api.flutter.dev".

{% include master-api.md %}

API documentation:

* [`ClassName`][]

Relevant issues:

* [Issue xxxx][]
* [Issue yyyy][]

Relevant PRs:

* [PR title #1][]
* [PR title #2][]
{% endcomment %}

{% comment %}
  Add the links to the end of the file in alphabetical order.
  The following links are commented out because they make
  the GitHubActions (GHA) link checker believe they are broken links,
  but please remove the comment tags before you commit!

  If you are sharing new API that hasn't landed in
  the stable channel yet, use the master channel link.
  To link to docs on the master channel,
  include the following note and make sure that
  the URL includes the master link (as shown below).

  Here's an example of defining a stable (site.api) link
  and a master channel (master-api) link.

<!-- Stable channel link: -->
[`ClassName`]: {{site.api}}/flutter/[link_to_relevant_page].html

<!-- Master channel link: -->
{% include master-api.md %}

[`ClassName`]: https://master-api.flutter.dev/flutter/[link_to_relevant_page].html

[Issue xxxx]: {{site.github}}/flutter/flutter/issues/[link_to_actual_issue]
[Issue yyyy]: {{site.github}}/flutter/flutter/issues/[link_to_actual_issue]
[PR title #1]: {{site.github}}/flutter/flutter/pull/[link_to_actual_pr]
[PR title #2]: {{site.github}}/flutter/flutter/pull/[link_to_actual_pr]
{% endcomment %}
