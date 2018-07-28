# Register a single textWatcher to multiple editTexts

Sometimes, we need to register a single ```TextWatcher``` got multiple ```Edittexts```. We can create a custom ```TextWatcher``` for that. Let's see how it all works.

* First let's create an interface called ```TextWatcherWithInstance```. It has 3 methods (the same methods as TextWatcher class).
```
public interface TextWatcherWithInstance {
        void beforeTextChanged(EditText editText, CharSequence s, int start, int count, int after);

        void onTextChanged(EditText editText, CharSequence s, int start, int before, int count);

        void afterTextChanged(EditText editText, Editable editable);
    }
```    

* Secondly, let's create an util class called ```MultiTextWatcher```. It has 2 methods. 
  * A method to register the multiple ```EditTexts``` called ```registerEditText```. This method will register a ```TextWatcher``` to the edittext passed as a parameter.  
  * A method to register the interface callback we have defined called ```setCallback```
```
public class MultiTextWatcher {

    private TextWatcherWithInstance callback;

    public MultiTextWatcher setCallback(TextWatcherWithInstance callback) {
        this.callback = callback;
        return this;
    }

    public MultiTextWatcher registerEditText(final EditText editText) {
        editText.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {
                callback.beforeTextChanged(editText, s, start, count, after);
            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                callback.onTextChanged(editText, s, start, before, count);
            }

            @Override
            public void afterTextChanged(Editable editable) {
                callback.afterTextChanged(editText, editable);
            }
        });

        return this;
    }
```    
  
  
* Our final class will look like this:

```
public class MultiTextWatcher {

    private TextWatcherWithInstance callback;

    public MultiTextWatcher setCallback(TextWatcherWithInstance callback) {
        this.callback = callback;
        return this;
    }

    public MultiTextWatcher registerEditText(final EditText editText) {
        editText.addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {
                callback.beforeTextChanged(editText, s, start, count, after);
            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                callback.onTextChanged(editText, s, start, before, count);
            }

            @Override
            public void afterTextChanged(Editable editable) {
                callback.afterTextChanged(editText, editable);
            }
        });

        return this;
    }

    public interface TextWatcherWithInstance {
        void beforeTextChanged(EditText editText, CharSequence s, int start, int count, int after);

        void onTextChanged(EditText editText, CharSequence s, int start, int before, int count);

        void afterTextChanged(EditText editText, Editable editable);
    }
}
```


We can use this single class in all apps and use it like this:
```
new MultiTextWatcher()
.registerEditText(editText)
.setCallback(new MultiTextWatcher.TextWatcherWithInstance() {
                    @Override
                    public void beforeTextChanged(EditText editText, CharSequence s, int start, int count, int after) {
                        
                    }

                    @Override
                    public void onTextChanged(EditText editText, CharSequence s, int start, int before, int count) {

                    }

                    @Override
                    public void afterTextChanged(EditText editText, Editable editable) {

                    }
                });
```                
