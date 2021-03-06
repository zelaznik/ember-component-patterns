# Select Element

## Introduction

As a general rule, the `<select>` element should be used \(instead of radio buttons\) if any of the following cases are true:

1. there are more than 5-7 choices
2. there are a large number of _familiar_ options available \(there is no need to be able to compare the options\)
3. the default choice is the recommended choice

It is a common misconception that the select element is not enough for web development. While there are some constraints on styling, the select element serves a specific purpose and should be used in those cases. It is ill-advised to disregard the select component entirely simply because it does not support more complex use cases out of the box. Instead, additional components should be created for complex functionality.

## Part One: Considering Markup

The select element markup is rather straight-forward- all of the options are grouped together in one list, or option groups are indicated.

```markup
<div class="form-select">
    <label for="select-color">Select your preferred color:</label>
    <select name="colorPrefs" id="select-color">
        <option value="red">Red</option>
        <option value="orange">Orange</option>
        <option value="yellow">Yellow</option>
        <option value="green">Green</option>
        <option value="blue">Blue</option>
        <option value="indigo">Indigo</option>
        <option value="violet">Violet</option>
    </select>
</div>
```

Benefits provided by the semantic html element:

* if a user clicks on the label, the focus will automatically go to the select element
* keyboard navigation is built in:
  * the `ENTER` key will toggle the dropdown open/closed
  * the arrow keys will navigate up and down the list
  * the `ESC` key will close the dropdown
* assistive technology \(like screen readers\) will already understand what this code is meant to do

If grouping is desired, the `<optgroup>` markup can be used:

```markup
<div class="form-select">
    <label for="select-activity">Select your preferred activity:</label>
    <select name="activityPrefs" id="select-activity">
        <optgroup label="Indoor">
            <option value="indoor-sewing">Sewing</option>
            <option value="indoor-painting">Painting</option>
            <option value="indoor-baking">Baking</option>
        </optgroup>
        <optgroup label="Outdoor">
            <option value="outdoor-climbing">Climbing</option>
            <option value="outdoor-hiking">Hiking</option>
            <option value="outdoor-horseback">Horseback Riding</option>
        </optgroup>
    </select>
</div>
```

While multiple selections can be allowed through the use of the `multiple` attribute -- most browsers will show a scrolling list box instead of a single line dropdown -- it should be noted that use of the `multiple` attribute is not generally advised, as it can be a confusing interface for users.

## Part Two: Creating the Ember Component

The component should be generated \(via ember-cli\):

```bash
ember generate component input-select -gc
```

This will create three files and put them in the correct location:

* app/components/input-select.hbs
* app/components/input-select.js
* tests/integration/components/input-select-test.js

In **app/components/input-select.hbs**, the component markup can be set up and the places where dynamic functionality is needed can be indicated.

So this markup:

```markup
<div class="form-select">
  <label for="selectId">Label Text</label>
  <select id="selectId" name="select-name">
    <option value="option-one">Option One</option>
    <option value="option-two">Option Two</option>
    <option value="option-three">Option Three</option>
  </select>
</div>
```

Becomes this component template:

```markup
<div class="form-select">
  <label for={{this.selectId}}>{{this.selectLabelText}}</label>
  <select id={{this.selectId}} name={{this.selectName}}>
    {{#each this.selectOptions as |selectOption|}}
      <option value={{selectOption}}>{{selectOption}}</option>
    {{/each}}
  </select>
</div>
```

In **app/components/input-select.js**, the select `id` will need to be generated so the label element can access it. While there are a few different ways to accomplish this, the existing `guidFor` function will serve nicely:

```javascript
import Component from '@glimmer/component';
import { guidFor } from '@ember/object/internals';

export default class InputSelectComponent extends Component {
  selectId = 'select-' + guidFor(this); 
  selectedOptions = ['optionOne', 'optionTwo', 'optionThree'];
  selectLabelText = 'Option List';
  selectName = 'optionList';
}
```

Then, the component can be used in the view or page template:

```markup
<InputSelect />
```

### Considering Attributes

Any form input planning should include considerations for which attributes should be supported. At the bare minimum, `required`, `disabled`, and `readonly` should be considered.

## Part Three: Abstracting for Reuse

To create something more flexible, allow for definition upon invocation:

### Template

```markup
<div class="form-select">
  <label for={{this.selectId}}>{{@selectLabelText}}</label>
  <select id={{this.selectId}} name={{@selectName}}>
    {{#each @selectOptions as |selectOption|}}
      <option value={{selectOption}}>{{selectOption}}</option>
    {{/each}}
  </select>
</div>
```

### Component

```javascript
import Component from '@glimmer/component';
import { guidFor } from '@ember/object/internals';

export default class InputSelectComponent extends Component {
  selectId = 'select-' + guidFor(this); 
}
```

### Invocation

```markup
<InputSelect
  @selectedLabelText="Option List Label Text"
  @selectedName="optionList"
  @selectedOptions={{array 'optionOne' 'optionTwo' 'optionThree'}} 
/>
```

## References

* [https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement)
* [https://blog.prototypr.io/7-rules-of-using-radio-buttons-vs-drop-down-menus-fddf50d312d1](https://blog.prototypr.io/7-rules-of-using-radio-buttons-vs-drop-down-menus-fddf50d312d1) 
* Listbox Keyboard Interaction: [https://www.w3.org/TR/wai-aria-practices/\#listbox\_kbd\_interaction](https://www.w3.org/TR/wai-aria-practices/#listbox_kbd_interaction)
* Styled Single Selects: [https://scottaohara.github.io/a11y\_styled\_form\_controls/src/select/](https://scottaohara.github.io/a11y_styled_form_controls/src/select/)

{% hint style="info" %}
Feedback is welcome! Visit the [GitHub repository for this project](https://github.com/MelSumner/ember-component-patterns) to raise an issue.
{% endhint %}

