---
description: Mirror Secret Sauce.
---

# Tests

A lot of developers are surprised by how stable Mirror is, compared to what they've used before.

This is not by coincidence. Mirror is **heavily** tested with:

* \> **1400** unit tests
* \~ **80%** test coverage

![\[2021-06-17\] Mirror Test Coverage of 79.6% - including all \[Obsoletes\]](<../../.gitbook/assets/2021-06-17 - 79,6 percent - including obsoletes.png>)

{% hint style="success" %}
As far as we know, **Mirror** has the highest test coverage of any `MonoBehaviour`networking library for **Unity**.&#x20;
{% endhint %}

In other words, 80% of our code is **covered with tests** making sure that for the given input, it always produces the correct output. Here is what this means in practice:

*   If you **report a bug**, we usually fix it and add a test to guarantee that it **never** happens again.&#x20;

    If we **accidentally** introduce a bug, odds are our tests will catch it immediately before you ever encounter it in our game.
* We can **improve** existing functions with confidence. If a rewrite doesn't produce exactly the same output as the previous version, then our tests will catch it.

{% hint style="success" %}
As **rule** **of** **thumb**, encountering a Mirror bug in production simply means that we haven't covered that part of the code with tests yet.
{% endhint %}

![](../../.gitbook/assets/2021-05-20\_16-06-57@2x.png)

If you download Mirror from the **Asset Store**, then you don't see those tests because we don't want you to worry about them. They are only on **GitHub**.

## Code Coverage Settings

To reproduce the Coverage results, use Unity's Code Coverage Package and run all of our Edit Mode tests.

![Code Coverage settings](../../.gitbook/assets/\_SETTINGS\_.png)

## MirrorTest

If you want to contribute tests or clean up existing ones, please do!

Check out the `MirrorEditModeTest` and `MirrorPlayModeTest` base classes. They provide some convenience functions and setup that we use for most of our tests. For example, Creating a networked object with some network components.



