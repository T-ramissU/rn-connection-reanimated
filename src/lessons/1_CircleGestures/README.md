# Circle Gestures

https://user-images.githubusercontent.com/2805320/236633815-8a714b8d-97d0-4b26-b04e-1e1b5bbf8c0d.MP4

## Step 1 – Add pan gesture to the knob

https://user-images.githubusercontent.com/2805320/236633812-e237aa2f-3608-4e26-b8fa-f8991d6a6355.mov

<details>
<summary>
  <b>[1]</b> Create a `Gesture.Pan` and apply it to the `knob` view using `GestureDetector`
</summary>

```jsx
const gesture = Gesture.Pan()

<GestureDetector gesture={gesture}>
  <Animated.View style={styles.knob} />
</GestureDetector>
```

</details>
<br />
<details>
<summary>
  <b>[2]</b> `gesture` should set a scale value based on which we are going to animate the knob. When gesture ends, we are bringing back the scale to the initial value
</summary>
  <br/>
<details>

<summary>
create a `scale` sharedValue starting from 1
</summary>

```jsx
const scale = useSharedValue(1);
```

</details>
<br />
<details>
<summary>
add onBegin method and change `scale` value using spring
</summary>

```jsx
.onBegin(() => {
  scale.value = withSpring(2)
})
```

</details>
<br />
<details>
<summary>
add onEnd method to bring back the scale to using spring
</summary>

```jsx
.onFinalize(() => {
  scale.value = withSpring(1)
})
```

</details>
<br />
<details>
<summary>
create a knobStyle using useAnimatedStyle and change the scale and borderWidth by interpolating scale.value `[1,2] → [layout.knobSize / 2, 2]`
</summary>

```jsx
const animatedStyle = useAnimatedStyle(() => {
  return {
    borderWidth: interpolate(
      scale.value,
      [1, 2],
      [layout.knobSize / 2, 2],
      Extrapolation.CLAMP,
    ),
    transform: [
      {
        scale: scale.value,
      },
    ],
  };
});
```

</details>
<br />
<details>
<summary>
apply this style to the knob
</summary>

```jsx
<Animated.View style={[styles.knob, animatedStyle]} hitSlop={hitSlop} />
```

</details>
<br />
<br/>
  </details>

## Step 2 – Use `useDerivedValue` to animate the knob

Instead of starting the animation in `onBegin`, we can use a boolean to track if the gesture is active or not. This way we can use `useDerivedValue` to animate the knob based on the gesture state.

<details>
<summary>
  <b>[1]</b> Create a `isInteracting` shared value and track the gesture state
</summary>

```jsx
export function CircleGesturesLesson() {
  const isInteracting = useSharedValue(false);

  const gesture = Gesture.Pan()
    .averageTouches(true)
    .onBegin(() => {
      isInteracting.value = true;
    })
    .onFinalize(() => {
      isInteracting.value = false;
    });

  /* ... */
}
```

</details>
<br/>
<details>
<summary>
  <b>[2]</b> Use `useDerivedValue` to animate the knob based on the `isInteracting` value
</summary>
  ```jsx
  const scale = useDerivedValue(() => {
    return withSpring(isInteracting.value ? 2 : 1);
  });
  ```

</details>
<br />
<br />
<br />

## Step 3 - Move the knob

https://user-images.githubusercontent.com/2805320/236633815-8a714b8d-97d0-4b26-b04e-1e1b5bbf8c0d.MP4

Using Pan gesture, we can get the `x` coordinate of the pan, assign it to a `sharedValue` that's starting from 0 and use this to apply the knob `translateX` position.

<details>
<summary>
  <b>[1]</b> `gesture` should set a `x` value based on which we are going to move/animate the knob. When gesture ends, we are bringing back the `x` to the initial value (0)
</summary>
  <br/>
<details>

<summary>
create a `x` sharedValue starting from 0
</summary>

```jsx
const x = useSharedValue(0);
```

</details>
<br />
<details>
<summary>
add onChange method and change x value based on `changeX`
</summary>

```jsx
.onChange((ev) => {
  x.value += ev.changeX
})
```

⚠️ TIP: The reason why we’re using `changeX` instead of `translationX` is that we would like to start from where we left when the gesture is triggered again (aka when we start panning again), in other words it keeps the knob in place and next time will move from the current position

</details>

<br />
<details>
<summary>
apply `transform.translateX` as style using `x` shared value
</summary>

```jsx
const animatedStyle = useAnimatedStyle(() => {
  return {
    borderWidth: //
    transform: [
      {
        translateX: x.value  // <--------- here
      },
      {
        scale: scale.value,
      },
    ],
  }
})
```

</details>
</details>
</>
<br />

## Next step

**Go to: [Balloon Slider](../2_BalloonSlider/)**
