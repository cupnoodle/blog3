---
layout: post
title:  "Android - show / hide keyboard"
date:   2017-03-02 10:20:10
permalink: "android-show-hide-keyboard"
---



I searched Stack Overflow for this question and  many of the result doesn't work or is too verbose.

Hence I post it here for my future reference.

```java
/**
 * Hides the soft keyboard
 */
public void hideSoftKeyboard() {
    if(getCurrentFocus()!=null) {
        InputMethodManager inputMethodManager = (InputMethodManager) getSystemService(INPUT_METHOD_SERVICE);
        inputMethodManager.hideSoftInputFromWindow(getCurrentFocus().getWindowToken(), 0);
    }
}

/**
 * Shows the soft keyboard
 */
public void showSoftKeyboard(View view) {
    InputMethodManager inputMethodManager = (InputMethodManager) getSystemService(INPUT_METHOD_SERVICE);
    view.requestFocus();
    inputMethodManager.showSoftInput(view, 0);
}
```

source : [http://stackoverflow.com/questions/18977187/how-to-hide-soft-keyboard-when-activity-starts](http://stackoverflow.com/questions/18977187/how-to-hide-soft-keyboard-when-activity-starts)