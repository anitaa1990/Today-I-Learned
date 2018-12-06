# How to change the height of a view using animation?

So there have been times when I needed to hide a view or show a view based on some requirement. Recently, I had to change the height of a view based on some result from api. But it seemed like a bad user experience to just change the height. So I added animation to it.</br></br> So let's see how to implement that.</br></br>


So I created a simple ```util``` class to acheive this animation. We use ```ValueAnimator``` to change the height from one value to another.
```
public static void slideView(View view,
                             int currentHeight,
                             int newHeight) {

  ValueAnimator slideAnimator = ValueAnimator
                .ofInt(currentHeight, newHeight)
                .setDuration(500);

  /* We use an update listener which listens to each tick 
   * and manually updates the height of the view  */
   
  slideAnimator.addUpdateListener(animation1 -> {
        Integer value = (Integer) animation1.getAnimatedValue();
        view.getLayoutParams().height = value.intValue();
        view.requestLayout();
   });

  /*  We use an animationSet to play the animation  */
  
      AnimatorSet animationSet = new AnimatorSet();
      animationSet.setInterpolator(new AccelerateDecelerateInterpolator());
      animationSet.play(slideAnimator);
      animationSet.start()
 }
```    

How to use this?
```
/* So we call the method slideView and pass the view. We also pass the currentHeight of the view 
 * and also the new height of the view.
 */
slideView(view, view.getLayoutParams().height, 1);
```

This is the output you would get:</br></br>
<img src="https://github.com/anitaa1990/Today-I-Learned/blob/master/media/android_slide_anim.gif" width="200" style="max-width:100%;">


