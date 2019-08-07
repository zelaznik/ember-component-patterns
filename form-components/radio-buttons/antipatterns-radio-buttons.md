---
description: These are patterns that should be avoided.
---

# Antipatterns: Radio Buttons

When considering how to craft a useful radio button group component, it is also necessary to consider what _not_ to do. If any Ember addon is evaluated for use, it is not only the Ember code itself that needs to be evaluated, but the output to the browser itself that should be evaluated. 

Consider this markup:

```markup
<div class="radio-button-group">
  <div class="radio-row">
    <div class="ember-view radio-button">
      <input class="ember-view radio-input" type="radio">
      <span class="form-label radio-label">True</span>
    </div>
  </div>
  <div class="radio-row">
    <div class="radio-button">
      <input class="radio-input" type="radio">
      <span class="form-label radio-label">False</span>        
    </div>
  </div>
</div>
```

This should be considered unacceptable for the following reasons: 

1. there is no `<label>` element associated with the `<input>` element
2. the radio buttons are not associated as a group with the use of the `name` attribute.
