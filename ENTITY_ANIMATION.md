# Entity Animation
## Animation Schema Types
An animation schema describes how an entity should be positioned as time passes.
For each node in the entity's skeleton, we define an animation timeline.
A timeline is sequence of "key frames".
A keyframe represents an important position; if this is a jumping animation,
then an example of a keyframe would be the peak of the jump.
When deciding how an entity should be positioned during an animation,
we will usually find ourselves between two keyframes.
For example, if our keyframes are at time fractions 0.0, 0.5 and 1.0,
and the current time fraction is 0.3, then we will need to find a middle ground
between the first and second keyframe.
This process is known as interpolating, or "lerping".
The default way of doing this is linear lerping; drawing a straight line between two points.
An alternative is splining; drawing a curve.

```js
export type AnimationSchema = {
    animationDurationMs: number
    loop?: boolean
    nodeAnimations?: NodeSkeletonAnimationSchema
}

type NodeSkeletonAnimationSchema = Record<NodeName, NodeAnimationSchema>

type NodeAnimationSchema = {
    timeline: AnimationTimelineSchema
}

type AnimationTimelineSchema = readonly KeyframeSchema[]

type KeyframeSchema = {
    timeFraction: number
    rotation?: LerpPointSchema // Rotations are assumed to be in radians.
}

/**
 * "pre" and "post" points exist to allow for discontinuities.
 */
export type LerpPointSchema =
    | Point
    | {
            lerpMode?: LerpModeSchema
            point: Point
      }
    | {
            lerpMode?: LerpModeSchema
            pre: Point // When lerping towards a point, we lerp towards its pre.
            post: Point // When lerping away from a point, we lerp away from its post.
      }

/**
 * "catmull-rom-spline" is a form of splining; drawing a curve between two points.
 */
export type LerpModeSchema = "linear" | "catmull-rom-spline"

/**
 * BLOCKBENCH ANIMATION SCHEMA TYPES
 *
 * We support native Blockbench animations. It should just be a case of copying and pasting
 * the specific animation from the exported JSON file.
 *
 * Notable differences:
 * - Blockbench animations do not use time fractions. Instead, they use absolute time.
 * - The unit of time is seconds, not milliseconds.
 * - The angular unit is degrees, not radians.
 */
export type BlockbenchAnimationSchema = {
    animation_length: number // The duration of the animation in seconds.
    loop?: boolean
    bones?: BlockbenchBonesAnimationSchema
}

type BlockbenchBonesAnimationSchema = Record<NodeName, BlockbenchBoneAnimationSchema>

type BlockbenchBoneAnimationSchema = {
    rotation?: BlockbenchAnimationTimelineSchema // Blockbench rotations are in degrees.
}

type BlockbenchAnimationTimelineSchema = Record<TimestampString, BlockbenchAnimationFrameSchema>

/**
 * "pre" and "post" points exist to allow for discontinuities.
 */
export type BlockbenchAnimationFrameSchema =
    | Point
    | {
            lerp_mode?: BlockbenchLerpModeSchema
            pre?: Point // When lerping towards a point, we lerp towards its pre.
            post: Point // When lerping away from a point, we lerp away from its post.
      }

/**
 * "catmullrom" is a form of splining; drawing a curve between two points.
 */
export type BlockbenchLerpModeSchema = "linear" | "catmullrom"

/**
 * The timestamp of the keyframe in seconds.
 */
type TimestampString = string

type Point = Vec3
```
