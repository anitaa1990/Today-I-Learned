# Load Bitmaps into Memory

I recently read an article on how to load bitmaps into memory in an efficient manner from [here](https://android.jlelse.eu/loading-large-bitmaps-efficiently-in-android-66826cd4ad53). Obviously, there are a lot of libraries that can care of this but I felt that knowing the theory behind it was helpful for me and I wanted to summarize the key points from the article here:

  * <b>How to load bitmap into Memory</b>: We can decode an image using ```BitmapFactory``` class.
    ```
    Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.image);
    imageView.setImageBitmap(bitmap);
    ```
    * The above code will fetch an image from the ```Drawable``` folder, <b>load it into memory</b> and display in the ```ImageView```. This means that when you check the image size when loaded into memory, you can see that it will be larger than the image size on disk. 
    * You can use ```bitmap.getByteCount()``` to check the image size.
    * This is because <b>the image is compressed when it is on disk (stored in a JPG, PNG, or similar format). Once you load the image into memory, it is no longer compressed and takes up as much memory as is necessary for all the pixels.</b>
    * In order to get the size of the image <i>without loading it into memory</i>: we can use ```BitmapFactory.Options```. 
      ```
      BitmapFactory.Options options = new BitmapFactory.Options();
      options.inJustDecodeBounds = true;   // -> We set inJustDecodeBounds to true to make sure that the image is not loaded to memory
      BitmapFactory.decodeResource(getResources(), R.mipmap.hqimage, options);
      ```
  * <b>How to reduce Image size in Memory</b>: We can use ```inSampleSize``` from the ```BitmapFactory.Options```. For instance, if we have an image with size 1000x1000 and if we set the ```inSampleSize``` to 2, then the image will be scaled to 500x500.
      ```
      BitmapFactory.Options options = new BitmapFactory.Options();
      options.inJustDecodeBounds = true;
      options.inSampleSize = 3;   // -> We can set a value to inSampleSize to scale the image
      BitmapFactory.decodeResource(getResources(), R.mipmap.hqimage, options);
      ```
      This step is done because: it’s not worth loading a 1024x768 pixel image into memory if it will eventually be displayed in a 128x96 pixel thumbnail in an ImageView. Instead of providing a constant value (which might not work since our images might be of different sizes), we can also calculate the ```inSampleSize``` value:
      ```
      public static int calculateInSampleSize(
            BitmapFactory.Options options, int reqWidth, int reqHeight) {
          // Raw height and width of image
          final int height = options.outHeight;
          final int width = options.outWidth;
          int inSampleSize = 1;

          if (height > reqHeight || width > reqWidth) {

              final int halfHeight = height / 2;
              final int halfWidth = width / 2;

              // Calculate the largest inSampleSize value that is a power of 2 and keeps both
              // height and width larger than the requested height and width.
              while ((halfHeight / inSampleSize) >= reqHeight
                      && (halfWidth / inSampleSize) >= reqWidth) {
                  inSampleSize *= 2;
              }
          }

          return inSampleSize;
      }
      ```
      
      Now we need to set the ```inJustDecodeBounds``` value to true again before we can display it in the ```ImageView```. The full code for the above implementation:
      ```
      BitmapFactory.Options options = new BitmapFactory.Options();
      options.inJustDecodeBounds = true;
      options.inSampleSize = calculateInSampleSize(options, 500,500);
      
      options.inJustDecodeBounds = false;
      Bitmap smallBitmap = BitmapFactory.decodeResource(getResources(), R.drawable.image, options);
      imageView.setImageBitmap(smallBitmap);
      ```
      Now if you check the bitmap size again, you will notice that it will have reduced significantly.
      
      
   * <b>How to reduce the size of image in disk</b>: We can use the ```compress``` method in the ```Bitmap``` class.       We can pass the bitmap type, the quality and the ```ByteArrayOutputStream``` to the ```compress``` method.
      ```
      ByteArrayOutputStream bos = new ByteArrayOutputStream();
      bitmap.compress(Bitmap.CompressFormat.JPEG, 100, bos);
      byte[] bitmapdata = bos.toByteArray();
      ```
      <b>Note:</b> The value 100 means the quality is not changed. If we set the value to 50, the image size would reduce significantly. 
      <b>Note:</b> Also, please keep in mind that quality cannot be changed in png format, only in jpeg format.
      
    





