# Material Design - Change button color in default Alert Dialog

Generally, all the widgets in an android app adhere to the ```colorPrimary``` value defined in the ```colors.xml``` file. So if we were to define an AlertDialog in the app, the text and the button color etc, will be the same as what is defined in the ```colorPrimary```. 

But I had a requirement recently where the primary color of the app was white but the alert Dialog button color needed to be different. 

* First define a new style in the ```styles.xml``` file.
  ```
  <style name="AlertDialogTheme" parent="Theme.AppCompat.Light.Dialog.Alert">
        <item name="colorPrimary">@color/colorPrimary</item>
  </style>
  ```
  
* Now when creating a new ```AlertDialog``` we can define the theme of the dialog:
  ```
  new AlertDialog.Builder(this, R.style.AlertDialogTheme)
    .setTitle("Alert Dialog Title")
    .setMessage("Alert Dialog Message")
    .setPositiveButton("Click ME", (dialogInterface, i) -> {
                    
  }).show();
  ```
