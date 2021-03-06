# Semantic-UI components for hyperdom

These are some simple wrappers for using [Semantic UI](http://semantic-ui.com/) with [hyperdom](https://github.com/featurist/hyperdom).

For the most part they're very simple - only calling the corresponding JS to setup the components, they still require you to generate the HTML for the presentation.

Components are written on an as-needed basis, so if you see one missing, please make a pull request.

```bash
npm install hyperdom-semantic-ui
```

## modal

```JavaScript
semanticUi.modal(options, vdom);
```

* `options` - options passed to `$(element).modal(options)`.
* `vdom` - contents of the modal.

```JavaScript
var hyperdom = require('hyperdom');
var h = hyperdom.html;
var semanticUi = require('hyperdom-semantic-ui');

function render(model) {
  var refresh = h.refresh;

  return h('div',
    h('input', {type: 'checkbox', binding: [model, 'show']}),
    model.show
    ? semanticUi.modal(
        {
          onHidden: function () {
            model.show = false;
          }
        },
        h('.ui.modal',
          h('i.close.icon'),
          h('.header', 'Modal Title'),
          h('.content',
            h('.image', 'An image can appear on left or an icon'),
            h('.description', 'A description can appear on the right')
          ),
          h('.actions',
            h('.ui.button', 'Cancel'),
            h('.ui.button', 'OK')
          )
        )
      )
    : undefined
  );
}

hyperdom.append(document.body, render, {});
```

## tab (unbound)

There are two forms of the tab control, this one doesn't bind the selected tab to the model, and renders _all_ of the tabs, even if they're not shown.

```JavaScript
semanticUi.tabs([options], vdom);
```

* `options` - options passed to `$(element).find('.item').tab(options)`.
* `vdom` - contents of the modal, should contain several `.item` elements.

```JavaScript
var hyperdom = require('hyperdom');
var h = hyperdom.html;
var semanticUi = require('hyperdom-semantic-ui');

function render(model) {
  var refresh = h.refresh;

  return h('div',
    semanticUi.tabs(
      h('.ui.top.attached.tabular.menu',
        h('a.item.active', {dataset: {tab: 'first'}}, 'First'),
        h('a.item', {dataset: {tab: 'second'}}, 'Second'),
        h('a.item', {dataset: {tab: 'third'}}, 'Third')
      )
    ),
    h('.ui.bottom.attached.tab.segment.active', {dataset: {tab: 'first'}}, 'First'),
    h('.ui.bottom.attached.tab.segment', {dataset: {tab: 'second'}}, 'Second'),
    h('.ui.bottom.attached.tab.segment', {dataset: {tab: 'third'}}, 'Third')
  );
}

hyperdom.append(document.body, render, {});
```

## tab (bound)

There are two forms of the tab control, this one binds the selected tab to the model, and only renders the tab that is actually shown.

```js
semanticUi.tabs(
  '.ui.tabular.menu',
  {
    binding: [model, 'selectedTab'],
    tabs: [
      {
        key: 'first',
        tab: h('a.item', 'First'),
        content: function () {
          return h('First Content');
        }
      },
      {
        key: 'second',
        tab: h('a.item', 'Second'),
        content: function () {
          return h('Second Content');
        }
      },
      {
        key: 'third',
        tab: h('a.item', 'Third'),
        content: function () {
          return h('Third Content');
        }
      }
    ]
  }
)
```

## checkbox

```js
semanticui.checkbox({binding: [model, 'property'},
  h('.ui.toggle.checkbox',
    h('input', {type: 'checkbox'}),
    h('label', label)
  )
)
```

## dropdown

```js
semanticUi.dropdown(
  {
    // dropdown options, passed to $(element).dropdown(options);
  },

  h('.ui.button',
    h('.ui.icon.input',
      h('input.prompt', {type: 'text', placeholder: 'search'}),
      h('i.search.icon')
    ),
    h('.results')
  )
)
```

## search

```js
semanticUi.search(
  {
    // search options, passed to $(element).search(options);
  },
  h('.ui.search',
    h('.ui.icon.input',
      h('input.prompt', {type: 'text', placeholder: 'search'}),
      h('i.search.icon')
    ),
    h('.results')
  )
)
```

## form validation

```js
function save(form) {
  return form.validate().then(function () {
    return http.put(user.href, user);
  });
}

semanticUi.form(
  {
    key: user.id,
    fields: {
      email: {
        identifier: 'email',
        rules: [{
          type: 'email',
          prompt: 'please enter a valid email address'
        }]
      }
    },
    inline : true
  },
  function (form) {
    return h('form.ui.form',
      h('.field',
        h('label', 'Email'),
        h('input', {type: 'text', binding: [user, 'email'], placeholder: 'Email', name: 'email'})
      ),
      h('.ui.button', {onclick: function () { return save(form); }}, 'save')
    );
  }
);
```

```js
var vdom = semanticUi.form([options], vdom | function (component) { return vdom; });
```

* `options` - settings to be passed to `.form(settings)`
* `options.v1` - set this to true to use Semantic UI v1.x.x forms.
* `vdom` - vdom for the form
* `vdomFunction` - function taking one argument, the form, and returning vdom for the form.
* `form.validate()` - a function to run the validation rules, returns either `undefined` if the form is valid, or an array of errors. See [`onFailure`](http://semantic-ui.com/behaviors/form.html#/settings).
