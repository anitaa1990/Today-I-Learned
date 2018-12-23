# Create a Background Switcher View using ImageSwitcher

So I recently had to implement the following gif in Android. </br></br>
<img src="https://github.com/anitaa1990/Today-I-Learned/blob/master/media/android_dagger_sample.gif" style="max-width:100%;">

I needed to update the Background of the Activity when the RecyclerView scrolls and a select item is at the center. So let's see how to implement this.</br>

<b>Step 1: Create an interface class</b>: </br>
This is to update the UI when a RecyclerView is focused at the center(i.e. when a single item from the list is at the center of the screen).
```
public interface RecyclerSnapItemListener {
    void onItemSnap(int position);
}
```

<b>Step 2: We make use of the ```SnapHelper``` class of ```RecyclerView```.</b> </br>
```SnapHelper``` is a helper class that helps in snapping any child view of the RecyclerView. We implement our own version of SnapHelper class. Android offers two variants for you: ```LinearSnapHelper``` and ```PagerSnapHelper```.</br>
* ```LinearSnapHelper``` snaps to the item that is closest to the middle of the RecyclerView.
* ```PagerSnapHelper``` offers similar behavior to a ViewPager but requires that your item views have their layout parameters set to MATCH_PARENT.</br></br>
I strongly recommend going through this [article](https://proandroiddev.com/android-recyclerview-snaphelper-19eaa9598da6) to understand more about ```SnapHelper```.

Now we are going to create a custom ```SnapHelper``` class extending ```LinearSnapHelper```. We are going to pass the interface we created in the previous step to the custom SnapHelper class.

```
public class PagerSnapHelper extends LinearSnapHelper {

    private RecyclerSnapItemListener recyclerSnapItemListener;
    private OrientationHelper mVerticalHelper, mHorizontalHelper;

    public PagerSnapHelper(RecyclerSnapItemListener recyclerSnapItemListener) {
        this.recyclerSnapItemListener = recyclerSnapItemListener;
    }

    @Override
    public void attachToRecyclerView(@Nullable RecyclerView recyclerView) throws IllegalStateException {
        super.attachToRecyclerView(recyclerView);
    }

    @Override
    public int[] calculateDistanceToFinalSnap(@NonNull RecyclerView.LayoutManager layoutManager,
                                              @NonNull View targetView) {
        int[] out = new int[2];

        if (layoutManager.canScrollHorizontally()) {
            out[0] = distanceToStart(targetView, getHorizontalHelper(layoutManager));
        } else {
            out[0] = 0;
        }

        if (layoutManager.canScrollVertically()) {
            out[1] = distanceToStart(targetView, getVerticalHelper(layoutManager));
        } else {
            out[1] = 0;
        }
        return out;
    }

    @Override
    public View findSnapView(RecyclerView.LayoutManager layoutManager) {

        if (layoutManager instanceof LinearLayoutManager) {

            if (layoutManager.canScrollHorizontally()) {
                return getStartView(layoutManager, getHorizontalHelper(layoutManager));
            } else {
                return getStartView(layoutManager, getVerticalHelper(layoutManager));
            }
        }

        return super.findSnapView(layoutManager);
    }


    @Override
    public int findTargetSnapPosition(RecyclerView.LayoutManager layoutManager, int velocityX, int velocityY) {

        View centerView = findSnapView(layoutManager);
        if (centerView == null)
            return RecyclerView.NO_POSITION;

        int position = layoutManager.getPosition(centerView);
        int targetPosition = -1;
        if (layoutManager.canScrollHorizontally()) {
            if (velocityX < 0) {
                targetPosition = position - 1;
            } else {
                targetPosition = position + 1;
            }
        }

        if (layoutManager.canScrollVertically()) {
            if (velocityY < 0) {
                targetPosition = position - 1;
            } else {
                targetPosition = position + 1;
            }
        }

        final int firstItem = 0;
        final int lastItem = layoutManager.getItemCount() - 1;
        targetPosition = Math.min(lastItem, Math.max(targetPosition, firstItem));
        
        /*   
         * You can see here that we find the targetPosition and pass that to the interface.
         * Using this targetPositon, we would be able to find out which item is at the center.
         */
        if(targetPosition >= 0) recyclerSnapItemListener.onItemSnap(targetPosition);
        return targetPosition;
    }

    private int distanceToStart(View targetView, OrientationHelper helper) {
        return helper.getDecoratedStart(targetView) - helper.getStartAfterPadding();
    }

    private View getStartView(RecyclerView.LayoutManager layoutManager,
                              OrientationHelper helper) {

        if (layoutManager instanceof LinearLayoutManager) {
            int firstChild = ((LinearLayoutManager) layoutManager).findFirstVisibleItemPosition();

            boolean isLastItem = ((LinearLayoutManager) layoutManager)
                    .findLastCompletelyVisibleItemPosition()
                    == layoutManager.getItemCount() - 1;

            if (firstChild == RecyclerView.NO_POSITION || isLastItem) {
                return null;
            }

            View child = layoutManager.findViewByPosition(firstChild);

            if (helper.getDecoratedEnd(child) >= helper.getDecoratedMeasurement(child) / 2
                    && helper.getDecoratedEnd(child) > 0) {
                return child;
            } else {
                if (((LinearLayoutManager) layoutManager).findLastCompletelyVisibleItemPosition()
                        == layoutManager.getItemCount() - 1) {
                    return null;
                } else {
                    return layoutManager.findViewByPosition(firstChild + 1);
                }
            }
        }

        return super.findSnapView(layoutManager);
    }

    private OrientationHelper getVerticalHelper(RecyclerView.LayoutManager layoutManager) {
        if (mVerticalHelper == null) {
            mVerticalHelper = OrientationHelper.createVerticalHelper(layoutManager);
        }
        return mVerticalHelper;
    }

    private OrientationHelper getHorizontalHelper(RecyclerView.LayoutManager layoutManager) {
        if (mHorizontalHelper == null) {
            mHorizontalHelper = OrientationHelper.createHorizontalHelper(layoutManager);
        }
        return mHorizontalHelper;
    }
}
```


<b>Step 3: Attach the RecyclerView in our UI to the custom ```SnapHelper``` class.</b></br>
```
SnapHelper startSnapHelper = new PagerSnapHelper(new RecyclerSnapItemListener() {
            @Override
            public void onItemSnap(int position) {
                MovieEntity movie = moviesListAdapter.getItem(position);
                /* here we get the movie item that is currently at the center.
                 * now we can just update the background of the screen simply by calling
                 * layout.setBackgroundImage(movie.getImage());
                 * OR
                 * we can use BackgroundSwitcherView to change the background image.
                 * backgroundSwitcherView.updateCurrentBackground(movie.getPosterPath());
                 */
            }
        });
/* 
 * Here we attach the recyclerView to the SnapHelper
 */        
startSnapHelper.attachToRecyclerView(binding.moviesList);
```
 </br> 
 </br> 
  
  
<b> Step 4: Add animation to the background layout when it is in focus</b>: </br> 
You must have noticed in the gif image that the background movies everytime a new image is set. This is done with a help of a custom class: ```BackgroundSwitcherView``` which extends ```ImageSwitcher```. ```ImageSwitcher``` is a ```ViewSwitcher``` that switches between two ImageViews when a new image is set on it. 

```
public class BackgroundSwitcherView extends ImageSwitcher {
    private final int[] NORMAL_ORDER = new int[]{0, 1};

    private int bgImageGap;
    private int bgImageWidth;

    private Animation bgImageInLeftAnimation;
    private Animation bgImageOutLeftAnimation;

    private Animation bgImageInRightAnimation;
    private Animation bgImageOutRightAnimation;

    private int movementDuration = 500;
    private int widthBackgroundImageGapPercent = 12;

    private AnimationDirection currentAnimationDirection;

    public BackgroundSwitcherView(Context context, AttributeSet attrs) {
        super(context, attrs);
        inflateAndInit(context);
    }

    public BackgroundSwitcherView(Context context) {
        super(context);
        inflateAndInit(context);
    }

    private void inflateAndInit(final Context context) {
        setChildrenDrawingOrderEnabled(true);
        DisplayMetrics displayMetrics = context.getResources().getDisplayMetrics();
        bgImageGap = (displayMetrics.widthPixels / 100) * widthBackgroundImageGapPercent;
        bgImageWidth = displayMetrics.widthPixels + bgImageGap * 2;

        this.setFactory(() -> {
            ImageView myView = new ImageView(context);
            myView.setScaleType(ImageView.ScaleType.CENTER_CROP);
            myView.setLayoutParams(new LayoutParams(bgImageWidth, LayoutParams.MATCH_PARENT));
            myView.setTranslationX(-bgImageGap);
            return myView;
        });

        bgImageInLeftAnimation = createBgImageInAnimation(bgImageGap, 0, movementDuration);
        bgImageOutLeftAnimation = createBgImageOutAnimation(0, -bgImageGap, movementDuration);
        bgImageInRightAnimation = createBgImageInAnimation(-bgImageGap, 0, movementDuration);
        bgImageOutRightAnimation = createBgImageOutAnimation(0, bgImageGap, movementDuration);
    }


    @Override
    protected int getChildDrawingOrder(int childCount, int i) {
        return NORMAL_ORDER[i];
    }

    private synchronized void setImageBitmapWithAnimation(Bitmap newBitmap, AnimationDirection animationDirection) {
        if (animationDirection == AnimationDirection.LEFT) {
            this.setInAnimation(bgImageInLeftAnimation);
            this.setOutAnimation(bgImageOutLeftAnimation);
            this.setImageBitmap(newBitmap);

        } else if (animationDirection == AnimationDirection.RIGHT) {
            this.setInAnimation(bgImageInRightAnimation);
            this.setOutAnimation(bgImageOutRightAnimation);
            this.setImageBitmap(newBitmap);
        }
    }


    /*
     * Call this method to update the background image.
     * Here we are passing an image url and using Picasso
     * downloading the image and then setting the background to it.
     * We could also pass a drawable image or a drawable resource
     * here if we wish.
     * */
    public void updateCurrentBackground(String imageUrl) {

        this.currentAnimationDirection = AnimationDirection.RIGHT;
        ImageView image = (ImageView) this.getNextView();
        image.setImageDrawable(null);
        showNext();

        if(imageUrl == null) return;

        Picasso.get().load(imageUrl)
                .noFade().noPlaceholder()
                .into(new Target() {
                    @Override
                    public void onBitmapLoaded(Bitmap bitmap, Picasso.LoadedFrom from) {
                        setImageBitmapWithAnimation(bitmap, currentAnimationDirection);
                    }
                    @Override
                    public void onBitmapFailed(Exception e, Drawable errorDrawable) {
                        System.out.println("@#@#@#@#@" + e.getMessage());
                    }
                    @Override
                    public void onPrepareLoad(Drawable placeHolderDrawable) { }
                });
    }


    /*
     * This method sets the Bitmap to the background.
     *
     * */
    private void setImageBitmap(Bitmap bitmap) {
        ImageView image = (ImageView) this.getNextView();
        image.setImageDrawable(null);

        int duration = 0;
        animate().alpha(0.0f).setDuration(duration).setListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animation) {

            }

            @Override
            public void onAnimationEnd(Animator animation) {
                image.setImageBitmap(bitmap);
                new Handler().postDelayed(() -> animate().alpha(0.4f).setDuration(duration), 200);
            }

            @Override
            public void onAnimationCancel(Animator animation) { }

            @Override
            public void onAnimationRepeat(Animator animation) { }
        });
        showNext();
    }


    /*
     * Call this method used to clear the background image
     * */
    public void clearImage() {
        ImageView image = (ImageView) this.getNextView();
        image.setImageDrawable(null);
        showNext();
    }

    public enum AnimationDirection {
        LEFT, RIGHT
    }


    private static Animation createBgImageInAnimation(int fromX, int toX, int transitionDuration) {
        TranslateAnimation translate = new TranslateAnimation(fromX, toX, 0, 0);
        translate.setDuration(transitionDuration);

        AnimationSet set = new AnimationSet(true);
        set.setInterpolator(new DecelerateInterpolator());
        set.addAnimation(translate);
        return set;
    }

    private static Animation createBgImageOutAnimation(int fromX, int toX, int transitionDuration) {
        TranslateAnimation ta = new TranslateAnimation(fromX, toX, 0, 0);
        ta.setDuration(transitionDuration);
        ta.setInterpolator(new DecelerateInterpolator());
        return ta;
    }
}
```


And that's it. We call this custom class from our xml file like this:
```
<com.example.BackgroundSwitcherView
    android:id="@+id/overlay_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

And we can update the image of the BackgroundSwitcher view by calling this:
```
backgroundSwitcherView.updateCurrentBackground(movie.getPosterPath());
```


