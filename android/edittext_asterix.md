## EditText - Change EditText password mask character to asterisk (*)

When we create an ```EditText``` and set the inputType to ```android:inputType="textPassword"```, the default character that is used to mask the input is a ```.```. 

Sometimes, we need to change the default character to something else like an ```*```. Let's see how it works.

* Create a custom class called ```PasswordCharSequence``` that inherits from ```CharSequence``` class. We can override the ```charAt``` method to return ```*```.

  ```
      private class PasswordCharSequence implements CharSequence {
          private CharSequence mSource;
          public PasswordCharSequence(CharSequence source) {
              mSource = source;
          }
        

        public char charAt(int index) {
            return '*';
        }
        
        public int length() {
            return mSource.length();
        }
        
        public CharSequence subSequence(int start, int end) {
            return mSource.subSequence(start, end);
        }
    }
    ```
    
    
* Create a new custom class called ```AsteriskPasswordTransformationMethod``` that inherits the ```PasswordTransformationMethod``` class. We can override the ```getTransformation``` method.
  
  ```
   public class AsteriskPasswordTransformationMethod extends PasswordTransformationMethod {

     @Override
     public CharSequence getTransformation(CharSequence source, View view) {
        return new PasswordCharSequence(source);
   }
  ```  
  
  </br>  </br>
  Our final class looks like this:
  
  ```
  public class AsteriskPasswordTransformationMethod extends PasswordTransformationMethod {

    @Override
    public CharSequence getTransformation(CharSequence source, View view) {
        return new PasswordCharSequence(source);
    }


    private class PasswordCharSequence implements CharSequence {
        private CharSequence mSource;
        
        public PasswordCharSequence(CharSequence source) {
            mSource = source;
        }
        
        public char charAt(int index) {
            return '*';
        }
        
        public int length() {
            return mSource.length();
        }
        
        public CharSequence subSequence(int start, int end) {
            return mSource.subSequence(start, end);
        }
     }
  };  

