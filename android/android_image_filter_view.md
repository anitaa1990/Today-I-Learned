# ImageFilterView in ConstraintLayout 2.0

So I recently had a requirement to add filters to an ImageView. And I literally stumbled upon [ImageFilterView](https://developer.android.com/reference/android/support/constraint/utils/ImageFilterView) which has been introduced in the new ConstraintLayout 2.0. Let's checkout how it works!

Step 1: we just add the constraintLayout dependency to the app: 
```
implementation 'androidx.constraintlayout:constraintlayout:2.0.0-alpha1'
```

Step 2: We start using ImageFilterView. This is just a subclass of ```ImageView```
```
<androidx.constraintlayout.utils.widget.ImageFilterView
                android:id="@+id/image_view"
                android:layout_width="0dp"
                android:layout_height="0dp"
                android:scaleType="fitStart"
                app:warmth="0.2"
                app:contrast="1.1"
                app:saturation="2.5"
                android:src="@drawable/ic_test"
                app:layout_constraintDimensionRatio="H,2:1.5"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent" />
```  

We have the option to change the warmth, the contract and the saturation of the ImageView.

```
imageview.setContrast(1.2);
imageview.setSaturation(2.2);
imageview.setWarmth(0.5);
```

And that's it! This is the output you would get:
</br></br>
<img src="https://github.com/anitaa1990/Today-I-Learned/blob/master/media/android_imagefilterview.gif" width="250" style="max-width:100%;">
