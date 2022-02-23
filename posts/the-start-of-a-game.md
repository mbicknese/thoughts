---
title: The start of a game
description: Creating a pop in animation to get back into game development.
date: 2022-02-23
tags:
- Games
- Unity3D
- C#
- Project RTS
layout: layouts/post.njk
reading-time: 183
---

I love using Unity3D because of its versatile interface. Over the years, I've learned to embrace the inspector. Creating less bloated behaviors and leaving the composition to the game design.

To warm up the memory and add a splash of awesomeness to the GIFs to come. Let's make a little behavior to pop in new objects (as opposed to them just suddenly being there.) I started creating a `PopIn` script but realized that name is too limiting. My second option was to go with `AnimateIn`, this time I had to conclude that that's too broad. I'm not going to implement a system to animate any property. So `ScaleIn` is the name we'll be using.

> There are plenty of "naming variables is the hardest part of development"-jokes out there. And yes, one can descend into a rabbit hole of finding the perfect name. But readability, especially for others and your future self, is ever so important. A simple example is `e` for both `error` and `event` in JavaScript. Introducing ambiguity to save four characters is a bad trade-off in my books.

After creating `ScaleIn.cs`, go ahead and remove the lines:

```csharp
using System.Collections;
using System.Collections.Generic;
```

Unity adds these lines to new scripts, but they're not needed by default. Now, I want to record the start time and scale; interpolate the scale against the elapsed time since its creation. That way, it will scale from 0% to 100% in 1 second in a linear fashion. I will build the first version using linear interpolation to start it simple. Unity has `Mathf.Lerp` for that.

```csharp
Mathf.Lerp(Vector3.zero, Vector3.one, elapsedTime);
```

Gave me the error that a `Vector3` is not a `float`. Which, you know, makes sense. So we'll need to do the maths ourselves. Luckily, it's pretty simple. Here's the full script so far.

```csharp
public class ScaleIn : MonoBehaviour
{
    private float m_StartTime;
    private Vector3 m_StartScale;
    
    void Start()
    {
        m_StartTime = Time.time;
        m_StartScale = transform.localScale;
    }

    void Update()
    {
        transform.localScale = Mathf.Min(Time.time - m_StartTime, 1) * m_StartScale;
    }
}
```

> I threw in a `Mathf.Min` because I predicted that the scaling would never stop. Basically, it picks the lowest of two given values. In this case, it's either the elapsed time under 1, after which 1 will be the smallest number, and it will stick with it.

> If you're wondering about the `m_` notation: https://stackoverflow.com/questions/13018189/what-does-m-variable-prefix-mean

Because we kept it simple, it works! (It's a superstition)

Now I remember Unity having a curve type. I'd like to use it to spice up our animation. Just the linear growth is not that interesting. [Unity3D's documentation](https://docs.unity3d.com/ScriptReference/EditorGUI.CurveField.html) is of the highest quality, and a quick Google search told me everything I needed. We came from an implied one-second linear growth. I'm going to add an `AnimationCurve` field to "animate" the scale. The same documentation page shows `scale.Evaluate` in an example. Without reading up on it, it seems like the method we're looking for. Instead of just using the elapsed time in a linear fashing, we're going to feed it to the `Evaluate` function to have it walk along our curve. This time, we do not have to clamp it as the curve takes care of that for us.

```csharp
transform.localScale = scale.Evaluate(Time.time - m_StartTime) * m_StartScale;
```

I got to admit, creating the curve I had envisioned in the editor was a bit more complicated than I expected. So, in the end, I settled for this one:

![a curve in the Unity editor](/thoughts/img/rts-animation-curve.png)

Which got me this effect

![flag animating into view](/thoughts/img/rts-animation-result.gif)

## Bonus points, clean up

After running the animation, the script is going to update and calculate the scale each frame. This is not a big problem per se, but might impact performance later on when we have thousands of objects with this behaviour. My first hope is on having some sort of "animation finished" event we could use. Sadly, it doesn't seem to exist. The `AnimationCurve` page does describe the `keys` property which returns all `Keyframe` objects of the animation. [A `Keyframe` has a `time` property](https://docs.unity3d.com/ScriptReference/Keyframe.html) which I can match with the elapsed time. Which should tell me that the animation has been completed.

In the update loop I'm going to check if elapsed time is greater than last keyframe time and if so, delete the component. I forgot how to delete components, so time for [a quick lookup](https://answers.unity.com/questions/378930/how-delete-or-remove-a-component-of-an-gameobject.html). Seems like I just have to call `Destroy(this)`. Giving us these lines

```csharp
// quick property to prevent repeating myself.
private float elapsedTime => Time.time - m_StartTime; 
// ...
if (elapsedTime > scale.keys.Last().time)
{
    Destroy(this);
}
```

And that's all!
