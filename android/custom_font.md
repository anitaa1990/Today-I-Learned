# Custom Fonts - How to implement FontFamily in Android

We have always needed to add custom fonts in our android apps. But it always involved initializing the font in code and setting it to each widget. 

But ```Android 8.0``` (API level 26) introduces a new feature, Fonts in XML, which lets you use fonts as resources. Let's look more into how to implement this.

* <b>First step</b>: Right-click the ```res``` folder and go to ```New > Android resource directory```. 
  The New Resource Directory window appears. In the Resource type list, select font, and then click OK.
  
  <img src="https://github.com/anitaa1990/Today-I-Learned/blob/master/media/android_font_1.png" width="650" style="max-width:100%;">
  
  
* <b>Second step</b>: Add your font files in the font folder

  <img src="https://github.com/anitaa1990/Today-I-Learned/blob/master/media/android_font_2.png" width="400" style="max-width:100%;">
  
  
* <b>Third step</b>: Add font to ```TextView``` in xml:
  ```
  <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:fontFamily="@font/montserrat_light" />
  ```
  
  Add font to ```TextView``` in code:
  ```
  Typeface typeface = getResources().getFont(R.font.montserrat_light);
  textView.setTypeface(typeface);
  ```
