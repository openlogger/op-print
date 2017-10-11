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

      /* css mixins: */

      --op-print-button-general: {
        /* defaults: */
        border: none;
        padding: 10px;
        cursor: pointer;
      }
      --op-print-button: {
        /* defaults: (none)*/
      }
      --op-cancel-button: {
        /* defaults: */
        display: none;
      }
      --op-preview-iframe: {
        /* defaults: */
        display: none;
        border: none;
        background-color: #fff;
        width: 100%;
        position: fixed;
        left: 0;
        top: 0;
        height: 100vh;
        z-index: 1000;
      }
    }
  </style>
  <template>
    <!-- print button with preview -->
    <op-print id="print-list" preview>
      <span>Print list</span>
      <span slot="print-button">Print list</span>
      <span slot="cancel-button">Cancel</span>
    </op-print>

    <!-- print button without preview (no cancel-button slot needed)-->
    <op-print id="print-entire-page">
      <span slot="print-button">Print page</span>
    </op-print>
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
