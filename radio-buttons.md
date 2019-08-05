---
description: The radio button input. To be used with Ember 3.13+
---

# \[WIP\] Radio Buttons

### Introduction

The radio button input is a simple component, but it is one that is very easy to do in a way that is detrimental to the performance of your application and to users with assistive technology. 

Consider something like Material Design: 

```markup
<div class="mdc-radio">
  <input
    class="mdc-radio__native-control"
    type="radio"
    id="city-chicago"
    name="city">
  <div class="mdc-radio__background">
    <div class="mdc-radio__outer-circle"></div>
    <div class="mdc-radio__inner-circle"></div>
  </div>
</div>
<label for="city-chicago">Chicago</label>
```

Just to make this one radio button in Material Design, the following would be needed:

| HTML | CSS | JavaScript |
| :--- | :--- | :--- |
| 6 elements | 66 selectors | 2,374 lines \(unminified\) |
| 9 attributes | 141 properties | 30k minified |
| DOM depth of 3 | 10k minified |  |

And this is just one radio button- any app will likely need more than just one. So now, considerations should be made for how the appropriate markup can be achieved in a much more efficient way. 

### Step One: Markup

Now consider this markup which uses semantic HTML to obtain the same effect:

```markup
<div class="radio">
  <input
    type="radio"
    id="city-chicago"
    name="city">
  <label for="city-chicago">Chicago</label>
</div>
```

This is all that is needed. 

If extra styling on the radio button is desired, the [pseudo elements in CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements), rather than extra markup in the DOM, should be used. 

Of course, there are more options that will need to be build into this pattern, so consideration for those should be planned for next, as it is preferred to plan out the component before building it.

#### Considering Attributes & Properties

First, consider the HTML attributes that might need to be added to this component \(in addition to the ones that were already indicated in the markup sample\). It should be observed that the boolean attributes in HTML work a little differently than they do in JS. For these attributes, they must be omitted all together to indicate a `false` value. If `true`, the attribute name is only present. Consider these examples for a disabled radio button:

Correct:

```markup
<input type="radio" disabled />
```

Incorrect:

```markup
<input type="radio" disabled="true" />
```

Additionally, consideration for the difference between `disabled` and `readonly` is important- when a form is submitted, information marked as `readonly` **will** be sent to the server upon submit, whereas information marked `disabled` **will not**. 

#### Radio Group

A radio button is, by nature, not going to be used alone \(in those instances, a checkbox is the correct choice\). It is reasonable, then, to consider how all the parts would work together in the whole. Here is an example that could be used to give a user an opportunity to indicate contact method preference:

```markup
<fieldset>
  <legend>Preferred Contact Method</legend>
  <div class="radio">
    <input
      type="radio"
      id="contact-email"
      name="contact-method"
      value="email">
    <label for="contact-email">Email</label>
  </div>
  <div class="radio">
    <input
      type="radio"
      id="contact-text"
      name="contact-method"
      value="text-message">
    <label for="contact-email">Text Message</label>
  </div>
  <div class="radio">
    <input
      type="radio"
      id="contact-twitter"
      name="contact-method"
      value="twitter">
    <label for="contact-email">Twitter DM</label>
  </div>  
</fieldset>
```

The power of CSS makes it possible to use completely semantic markup and maintaining a separation of concerns where design is concerned- this means we do not need to use markup for styling. 

\(TODO: insert some examples of how this markup could be styled differently to achieve different visual designs\)

> Note: it is common to have an option pre-selected for users. An agreeable default selection can lower the amount of time a user spends filling out a form, which is always a bonus! To increase user happiness, try to re-visit the options once you have gathered some data. Observe which option users tend to choose the most, and make that the default option.

Now that well-crafted \(semantic, performant, and accessible\) markup exists, it can be converted into an Ember Component. 

### Step Two: the Ember Component

Generate the component:

```bash
$ ember generate component radio-group
```

In the generated template/component file, add this markup:

```markup
<fieldset>
  <legend>{{@title}}</legend>
  {{#each @user.contactPrefs as |contactPrefs| }}
    <div class="radio">
      <input
        class={{if this.isDisabled "disabled"}}
        id="contact-{{@contactPrefs.value}}"
        name={{@contactPrefs.name}}
        type="radio"
        value={{@contactPrefs.value}}
        {{if this.hasAutofocus autofocus}}
        {{if this.isDisabled disabled}}  
        {{if this.isReadOnly readonly}}
        {{if this.isRequired required}}
        {{if this.isSelected checked}}
      />
      <label for="contact-{{@contactPrefs.value}}">
        {{@contactPrefs.value}}
      </label>
    </div>
  {{/each}}  
</fieldset>
```

The component can then be added to the template: 

```markup
<RadioGroup 
  @radio-title="Preferred Contact Method"
  <!-- TODO -->
/>
```

#### Additional Considerations

If it is desirable to allow some attributes to be changed when the component is placed into the template, the specific placement of `...attributes` will allow the author to indicate which attributes can be changed and which cannot. To learn more about this, visit [https://octane-guides-preview.emberjs.com/release/upgrading/editions/\#toc\_attributes](https://octane-guides-preview.emberjs.com/release/upgrading/editions/#toc_attributes). 

### References

1. [Excessive DOM Size](https://developers.google.com/web/tools/lighthouse/audits/dom-size) \(Audit reference, performance\)
2. [MDN Reference: Radio Buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/radio)
3. Talk: [The Intersection of Performance and Accessibility](https://noti.st/ericwbailey/Yfyaxa)
4. [HTML Standard: Boolean Attributes](https://html.spec.whatwg.org/multipage/common-microsyntaxes.html#boolean-attributes)
5. [Pseudo Elements in CSS](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)
6. TODO some style references
