- Start Date: 2020-03-13
- RFC PR: https://github.com/ReCreateJS/rfcs/pull/5
- Relevant Project: EaselJS

# Stage Transforms

## Summary

Transforming the CreateJS stage, for example scaling the stage, causes globalToLocal, localToGlobal, and mouse position to not behave as expected.  This has led to confusion in the community and extra code to remedy.

## Motivation

Traditional practice has been to not scale the stage, but when CreateJS is exported from Adobe Animate, the stage is being scaled to accommodate the devicePixelRatio. Scaling in this manner definitely gives better visual result when the canvas is further scaled with CSS style.

## Detailed design

Here is a Web page that shows what happens when the CreateJS stage is scaled.

https://zimjs.com/createjs/retina.html

STAGE SCALING:<br>
The canvas is created at a desired width and height. The stage is made at that width and height and then scaled by the window.devicePixelRatio.  The canvas width and height properties are then set to match the new stage size.  The canvas is finally scaled with style back to the original width and height.

This technique is used by Adobe when publishing from Animate to CreateJS.  I am not sure if it was GSkinner who recommended it but I suspect it was added by Adobe without realizing what it would do to CreateJS.

Here is an example of the visual difference in ZIM which is built on CreateJS.  ZIM introduced this type of scaling in 10.3.0 and termed it Retina!  You can see that there is quite a remarkable and magical difference. It is well worth doing.

https://zimjs.com/retina.html<br>
https://zimjs.com/retina_off.html

WHAT BREAKS:<br>
In the https://zimjs.com/createjs/retina.html example, we show two circles following the mouse using the event object stageX and stageY properties.  The blue circle uses the stageX and stageY properties directly and does not follow the mouse properly.  The red circle has these properties divided by the stage scaleX and scaleY and does follow the mouse properly.

There is also a Container called holder placed at 200, 200 with a Shape rectangle(0,0,100,50) inside. We ask for the holder.localToGlobal(100, 50) and place a black circle on the stage at that location.  The circle is not in the right place.  We would expect it to be visually placed at the right bottom corner of the rectangle. We fixed this with a violet circle by dividing the resulting localToGlobal point by the stage scaleX and scaleY.

Rotating the stage introduces similar problems.  Skewing the stage probably would as well.

EXTERNAL SOLUTION:<br> 
Aside from dividing by stage scaleX and scaleY which works for scale, the resulting points can be transformed to accommodate the stage transformations.  Lanny posted this solution for the mouse position:

```
function mousedown(e){
	var global = stage.localToGlobal(e.target.x, e.target.y);
	e.target.offset = {x:global.x-e.stageX, y:global.y-e.stageY};
}
function mousemove(e){
	var local = stage.globalToLocal(e.stageX+e.target.offset.x, e.stageY+e.target.offset.y);
	e.target.x = local.x;
	e.target.y = local.y;
}
```

INTERNAL SOLUTION:<br> 
It would be better to bring this type of solution into CreateJS so developers get what they expect.  Here is a test page using modified createjs code.  The fixes will be added to a pull request after discussed here.

https://zimjs.com/createjs/retina2.html

The blue circle shows a correct cursor rollover and can be dragged to follow the mouse using an unmodified event object stageX and stageY.  The red circle is following rawX and rawY.  The circles also work with stagemousemove and when the stage is rotated (commented out in the code). 

The rectangles show localToGlobal, globalToLocal and localToLocal all working as expected in a container.  The container was scaled and rotated and cursor checked as well - all working.  Skew and multiple containers were not tested.

CREATEJS CHANGES:<br> 
Example changes can be found in here: https://zimjs.com/cdn/1.3.0/createjs_doc.js

The general technique to fix these internally was provided in an existing comment in CreateJS on the Stage class on line 8707.  This is an invert() on a transform:

```
// TODO: account for stage transformations?
// this._mtx = this.getConcatenatedMatrix(this._mtx).invert();
// var pt = this._mtx.transformPoint(o.x, o.y);
var evt = new createjs.MouseEvent(type, bubbles, false, pt.x, pt.y, etc.
target.dispatchEvent(evt);
```

Instead of the change being made here, this technique was used in the following places in the modified CreateJS file with some being an invert() and some being a regular transform() adjustment:

```
// line 6697 - localToGlobal (invert transform)
// line 6726 - globalToLocal (transform)
// line 6866 - hitTest (transform) - not being used see _getObjectsUnderPoint
// line 7761 - _getObjectsUnderPoint (transform)
// line 7784 - _getObjectsUnderPoint - small adjust
// line 8518 - _updatePointerPosition (invert transform)
```

A property on the createjs namespace was made on line 40:

```
createjs.stageTransformable = true;
```

Setting this to false will revert back to earlier CreateJS functionality. This property can also be read by code using createjs to adjust accordingly.  For example, in ZIM, we have already adjusted for stage scale.  We will now read this property and if it is true, we will not adjust for stage scale otherwise there would be a double adjustment.

## How we teach this

The stageTransformable property would go into the documentation along with an explanation.  Various forum questions or reports could be answered accordingly.  Otherwise, people would expect CreateJS to work in this manner so there is no need for teaching in general.

## Drawbacks

This will break existing code that solved the problem externally but can be turned off to not break existing code.  To update to a new version of CreateJS but keep the existing code working, the stageTransformable could be set to false:

```
createjs.stageTransformable = false;
```

There could be issues with Adobe Animate export to CreateJS code for internal MovieClip creation, etc. 


## Alternatives

The proposed solution may not be the best solution.  It may be that someone who really knows what is going on with matrix transformations could reprogram the Matrix to automatically start at the stage transformation.  If this is possible, perhaps only a change to the Matrix or maybe getConcatonatedMatrix would solve the issues.

## Unresolved questions

1. A Matrix expert should be consulted to make sure there is not a better solution - see Alternatives.

2. Testing on Adobe Animate output should be conducted and especially with MovieClips.

3. Skew and multiple nested and transformed containers should be tested.

4. The name of the createjs.stageTransformable property may be slightly misleading as the stage can still be transformed even if it is set to false.  What it means is that stage transformation effects will be accounted for.  Possibly we could prevent the stage from being transformed if stageTransformable is not set to true.

