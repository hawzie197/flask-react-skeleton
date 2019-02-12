# Libre Food Pantry Coding Standards


## Code style

The [Google JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html) is the
basis for our coding style, with additional guidance here where that style guide is not aligned with
ES6 or TypeScript.

## Coding practices

### General

#### Write useful comments
Comments that explain what some block of code does are nice; they can tell you something in less
time than it would take to follow through the code itself.

Comments that explain why some block of code exists at all, or does something the way it does,
are _invaluable_. The "why" is difficult, or sometimes impossible, to track down without seeking out
the original author. When collaborators are in the same room, this hurts productivity.
When collaborators are in different timezones, this can be devastating to productivity.

For example, this is a not-very-useful comment:
```ts
// Set default tabindex.
if (!$attrs['tabindex']) {
  $element.attr('tabindex', '-1');
}
```

While this is much more useful:
```ts
// Unless the user specifies so, the calendar should not be a tab stop.
// This is necessary because ngAria might add a tabindex to anything with an ng-model
// (based on whether or not the user has turned that particular feature on/off).
if (!$attrs['tabindex']) {
  $element.attr('tabindex', '-1');
}
```

In TypeScript code, use JsDoc-style comments for descriptions (on classes, members, etc.) and
use `//` style comments for everything else (explanations, background info, etc.).

In SCSS code, always use `//` style comments.

In HTML code, use `<!-- ... -->` comments, which will be stripped when packaging a build.


### API Design

#### Boolean arguments
Avoid adding boolean arguments to a method in cases where that argument means "do something extra".
In these cases, prefer breaking the behavior up into different functions.

```ts
// AVOID
function getTargetElement(createIfNotFound = false) {
  // ...
}
```

```ts
// PREFER
function getExistingTargetElement() {
  // ...
}

function createTargetElement() {
 // ...
}
```


#### JsDoc comments

All public APIs must have user-facing comments. 

Private and internal APIs should have JsDoc when they are not obvious. Ultimately it is the purview
of the code reviewer as to what is "obvious", but the rule of thumb is that *most* classes,
properties, and methods should have a JsDoc description.


Methods blocks should describe what the function does and provide a description for each parameter
and the return value:
```ts
  /**
   * Opens a modal dialog containing the given component.
   * @param component Type of the component to load into the dialog.
   * @param config Dialog configuration options.
   * @returns Reference to the newly-opened dialog.
   */
  open(component, config) { ... }
```

Boolean properties and return values should use "Whether..." as opposed to "True if...":
```ts
  /** Whether the button is disabled. */
  disabled = false;
```

#### Try-Catch

Avoid `try-catch` blocks, instead preferring to prevent an error from being thrown in the first
place. When impossible to avoid, the `try-catch` block must include a comment that explains the
specific error being caught and why it cannot be prevented.


#### Naming

##### Classes
Classes should be named based on what they're responsible for. Names should capture what the code
*does*, not how it is used:
```
/** NO: */
class RadioService { }

/** YES: */
class UniqueSelectionDispatcher { }
```

##### Methods
The name of a method should capture the action that is performed *by* that method rather than
describing when the method will be called. For example,

```ts
/** AVOID: does not describe what the function does. */
handleClick() {
  // ...
}

/** PREFER: describes the action performed by the function. */
activateRipple() {
  // ...
}
```

### CSS

#### Be cautious with use of `display: flex`
* The [baseline calculation for flex elements](http://www.w3.org/TR/css-flexbox-1/#flex-baselines)
is different than other display values, making it difficult to align flex elements with standard
elements like input and button.
* Component outermost elements are never flex (block or inline-block)
* Don't use `display: flex` on elements that will contain projected content.

#### Use lowest specificity possible
Always prioritize lower specificity over other factors. Most style definitions should consist of a
single element or css selector plus necessary state modifiers. **Avoid SCSS nesting for the sake of
code organization.** This will allow users to much more easily override styles.

For example, rather than doing this:
```scss
.mat-calendar {
  display: block;

  .mat-month {
    display: inline-block;

    .mat-date.mat-selected {
      font-weight: bold;
    }
  }
}
```

do this:
```scss
.mat-calendar {
  display: block;
}

.mat-calendar-month {
  display: inline-block;
}

.mat-calendar-date.mat-selected {
  font-weight: bold;
}
```

#### Never set a margin on a host element.
The end-user of a component should be the one to decide how much margin a component has around it.

#### Prefer styling the host element vs. elements inside the template (where possible).
This makes it easier to override styles when necessary. For example, rather than

```scss
the-host-element {
  // ...

  .some-child-element {
    color: red;
  }
}
```

you can write
```scss
the-host-element {
  // ...
  color: red;
}
```

The latter is equivalent for the component, but makes it easier override when necessary.

#### Support styles for Windows high-contrast mode
This is a low-effort task that makes a big difference for low-vision users. Example:
```css
@media screen and (-ms-high-contrast: active) {
  .unicorn-motocycle {
    border: 1px solid #fff !important;
  }
}
```

#### Explain what CSS classes are for
When it is not super obvious, include a brief description of what a class represents. For example:
```scss
// The calendar icon button used to open the calendar pane.
.mat-datepicker-button { ... }

// Floating pane that contains the calendar at the bottom of the input.
.mat-datepicker-calendar-pane { ... }

// Portion of the floating panel that sits, invisibly, on top of the input.
.mat-datepicker-input-mask { }
```

