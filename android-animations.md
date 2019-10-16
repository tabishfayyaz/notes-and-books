
### Some Notes on Android Animation

- BitmapFactory.Options (inSampleSize) for bitmap scaling
- requestLayout (should be done after adding/removing a view)
- 2 ways to do list view animations (ViewPropertyAnimator or view.setHasTransientState())	
- ValueAnimator for bouncing sheep (setRepeatCount, setRepeatMode, setInterpolator)
	- one can invalidate specific sub-areas of a screen View.invalid()
	ValueAnimator is the timing engine behind ViewPropertyAnimator/ObjectAnimator
- Property Animations (alpha, translate, rotate, scale) done using ObjectAnimator
	- ValueAnimator.REVERSE is used for reversing the animation back to original position
	- PropertyValuesHolder used for scale animation
	- AnimatorSet used for playing animations in sequence
	- ObjectAnimator or AnimatorSet can written in xml file as well (AnimatorInflater.loadAnimator())
- KeyFrame animation: AnimationDrawable can be created all in code (addFrame, setOneShot), what is used in momentous
- TransitionDrawable for simple cross fade (startTransition, reverseTransition)
- Window animation (default activity animation, translate, scale animation)
	- ActivityOptions.makeCustomAnimation(….).toBundle();
	- overridePendingTransition(custom animation xml);
	- ActivityOptions.makeScaleUpAnimation(….).toBundle();
	- ActivityOptions.makeThumbnailScaleUpAnimation(….).toBundle();
	- avoid doing custom animation for MAIN launch activity, you can do it for activities you launch in code
- Animation.RELATIVE_TO_PARENT, Animation.ABSOLUTE, Animation.RELATIVE_TO_SELF, TranslateAnimation, RotateAnimation, ScaleAnimation
- Bitmap Allocation: BitmapFactory.Options().inBitmap (bitmaps should be same size)
- Layout Transitions: while adding/removing custom views
	- creates a custom color view, android:animateLayoutChanges=“true” on LinearLayout for example (3.1) 
	- LayoutTransition transition = linearLayout.getLayoutTransition (4.1)
	  transition.enableTransitionType(LayoutTransition.CHANGING)
- Picture Viewer: all views have their own viewpropertyanimator (.animate())
	- .animate().withLayer() 
	- withEndAction
- Animating Multiple Properties in Parallel: 
	- wrong way is to use ValueAnimator e.g. ofFloat(0, 400) and do setTranslationX setTranslationY separately
	- good approach: view.animate().translationX(x).translationY(y)
	- if you can’t use ViewPropertyHolder (when animating things that aren’t properties on a view) 
	  then PropertyValueHolder with ObjectAnimator and ValueAnimator
	- ObjectAnimator.ofPropertyValuesHolder(view, property1, property2, property3).start();
- Curved Motion: Chet Haase has blogged about this as well, AnimatorPath, AnimatorPath.curveTo(….),
- Anticipation and Overshoot 1: OvershootInterpolator(10f), DecelerateInterpolator
		- .animate.setInterpolator(Overshooter)
- Anticipation and Overshoot 2: Demo of a animating user defined property (skewX)
- Squash and Stretch:	
	- ObjectAnimator.setRepeatMode(ValueAnimator.REVERSE), View.setPivotX, setPivotY
	- PropertyValuesHolder.ofFloat(View.SCALE_X, 2);		
- AccelerateInterpolator: start slow end faster
- DecelerateInterpolator: start fast end slow

Source: https://www.youtube.com/playlist?list=PLWz5rJ2EKKc86y1CjAlexivfvOms6_0NC

