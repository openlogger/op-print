# \<op-print\>

Print a controllable section of a page

## Install the Polymer-CLI

First, make sure you have the [Polymer CLI](https://www.npmjs.com/package/polymer-cli) installed. Then run `polymer serve` to serve your element locally.

## Usage

```html
<link rel="import" href="../bower_components/polymer/polymer-element.html">
<link rel="import" href="../bower_components/op-print-element/op-print.html">

<dom-module id="x-custom">
  <style media="screen">
    op-print {
      margin-top: 10px;

      /* styling applied to button element */
      --op-print-button: {
        background: #6f98f6;
        color: #fff;
        text-transform: uppercase;
      }
    }
  </style>
  <template>
    <op-print id="print-list">Print list</op-print>
    <op-print id="print-entire-page">Print entire page</op-print>
  </template>

  <script>
    class xCustom extends Polymer.Element {

      static get is() { return 'x-custom'; }

      static get properties() {
        return {
          listExample: {
            type: Array,
            value: () => {
              return [{
                index: 1,
                value: {
                  condition: true,
                  subvalue: 'First item',
                  sublist: [{
                    name: 'I am sub 1!'
                  }, {
                    name: 'I am sub 2!'
                  }]
                }
              }, {
                index: 2,
                value: {
                  condition: false,
                  subvalue: 'Second item',
                  sublist: [{
                    name: 'I am sub 1!'
                  }]
                }
              }]
            }
          }
        }
      }

      constructor() {
        super();
        this._boundPrintList = this.printList.bind(this);
        this._boundPrintPage = this.printPage.bind(this);
      }

      connectedCallback() {
        super.connectedCallback();

        // events are named after op-print element id: '[id-attribute]:print'
        window.addEventListener('print-list:print', this._boundPrintList);
        window.addEventListener('print-entire-page:print', this._boundPrintPage);
      }

      disconnectedCallback() {
        super.disconnectedCallback();
        window.removeEventListener('print-list:print', this._boundPrintList);
        window.removeEventListener('print-entire-page:print', this._boundPrintPage);
      }

      printList(e) {

        // object properties
        let listTpl = '<h2>{{index}}: {{value.subvalue}}</h2>';

        // if conditions (Cannot be nested!)
        listTpl += '{{@if value.condition}}';
        listTpl += '<p>The condition is met!</p>';
        listTpl += '{{/if}}';

        // each loop (Cannot be nested!)
        listTpl += '{{@each value.sublist}}';
        listTpl += '<p>{{name}}</p>';
        listTpl += '{{/each}}';

        // e.detail.printer.print( data, options )
        // data is an array of strings or objects, options is an object
        e.detail.printer.print([
            '<h1>A string</h1>', // strings are put as is
            {
              type: 'ul', // 'ul' | 'ol'
              class: 'list', // css class
              tpl: listTpl, // template, see example above
              items: this.listExample, // array of objects
            },
            '<footer>@ 2017</footer>',
          ],
          {
            omitHeader: true, // true | false, default false
            omitDocumentTitle: true, // true | false, default false
            logo: '../images/logo.svg', // logo source for header, default ''
            title: 'Print example', // string
            stylesheets: ['../src/print-styles/common.css'] // array of style urls, default []
          }
        );
      }

      printPage(e) {
        // with no arguments, the entire page is printed
        e.detail.printer.print();
      }
    }

    window.customElements.define(xCustom.is, xCustom);
  </script>
</dom-module>
```


## Viewing Your Element

```
$ polymer serve
```

Your application is already set up to be tested via [web-component-tester](https://github.com/Polymer/web-component-tester). Run `polymer test` to run your application's test suite locally.
