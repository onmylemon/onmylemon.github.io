---
layout: post
title: Writing an Editor for SlickGrid
description: "Slickgrid editors can be easy to write."
tags: [javascript, slickgrid, snippets]
image:
  feature: featured/2014-06-20-writing-an-editor-for-slickgrid.png
comments: true
share: true
---
<a title="SlickGrid" href="https://github.com/mleibman/SlickGrid" target="_blank">SlickGrid</a> is a data grid used to display tabular information is a user and programmer friendly way. In recent months I have become possibly too familiar with SlickGrid and although it can be annoying on occasion, I have to admit it is a well written and executed Javascript library.<!-- more -->
<h3>How SlickGrid handles data</h3>
SlickGrid makes use of a data provider called the DataView and the grid itself, called the DataGrid. The DataView is responsible for holding the data for all the cells in the table, it is possible to implement your own DataView as long as you make certain methods available via a public api. Both the DataView and the DataGrid provide events that can be used to trigger updates of the data, column information or any other aspect of either view or grid.

The editor used for a particular cell is defined in the definition of the column that cell is in, here is an example making use of validation, an editor, and a formatter to manipulate and display the data in each cell.

{% highlight javascript %}
{
    id          : "1",
    field       : "SortCode",
    name        : "Bank Sort Code *",
    validator   : "^[0-9]{6}$"
    editable    : true,
    editor      : SlickGrid.Editors.TextEditor,
    formatter   : SlickGrid.Formatters.TextFormatter
}
{% endhighlight %}

<h3>Structure of an Editor</h3>
A SlickGrid editor is a function that implements certain methods in order to provide a way of editing data stored in the grid and passing that information back to the data view. The methods that it must make publicly available are:
<ul>
	<li>destroy - Remove all DOM elements, events and data created by the constructor</li>
	<li>focus - Used to set input focus</li>
	<li>isValueChanged - Returns a boolean if the value has been changed by the editor</li>
	<li>serializeValue - Returns a serialized version of the data from the editor</li>
	<li>loadValue - Updates the value of the input or any other field after the editor is initialised</li>
	<li>applyValue - Sets the value in the grid data once the editor has been destroyed</li>
	<li>validate - Validate user input using any method wanted, returns and object with valid: boolean and msg: string</li>
</ul>
There are a few optional methods you can implement as well:
<ul>
	<li>show - Triggered when a cell being edited is scrolled into view</li>
	<li>hide - Triggered when a cell being edited scrolls out of view</li>
	<li>position - Triggered when scrolling the grid</li>
</ul>
As long as you pass a constructor function that implements the required methods you have created a SlickGrid editor
<h3>The most basic editor</h3>
The most basic of editors is one that just provides a form input for information to be entered and stored in the view and grid when exiting the field. This is pretty much lifted straight from the SlickGrid in built <a title="SlickGrid: Editors" href="https://github.com/mleibman/SlickGrid/blob/master/slick.editors.js" target="_blank">editor file</a>.

{% highlight javascript %}
function TextEditor(args) {
    var $input;
    var defaultValue;
    var scope = this;

    this.init = function() {
        $input = $("<input />")
            .appendTo(args.container)
            .bind("keydown.nav", function(e) {
                if (e.keyCode === $.ui.keyCode.LEFT || e.keyCode === $.ui.keyCode.RIGHT) {
                    e.stopImmediatePropagation();
                }
            })
            .focus()
            .select();
    };

    this.destroy = function() {
        $input.remove();
    };

    this.focus = function() {
        $input.focus();
    };

    this.getValue = function() {
        return $input.val();
    };

    this.setValue = function(val) {
        $input.val(val);
    };

    this.loadValue = function(item) {
        defaultValue = item[args.column.field] || "";
        $input.val(defaultValue);
        $input[0].defaultValue = defaultValue;
        $input.select();
    };

    this.serializeValue = function() {
        return $input.val();
    };

    this.applyValue = function(item, state) {
        item[args.column.field] = state;
    };

    this.isValueChanged = function() {
        return (!($input.val() == "" && defaultValue == null)) && ($input.val() != defaultValue);
    };

    this.validate = function() {
        if (args.column.validator) {
            var validationResults = args.column.validator($input.val());
            if (!validationResults.valid) {
                return validationResults;
            }
        }

        return {
            valid: true,
            msg: null
        };
    };

    this.init();
}
{% endhighlight %}

<h3>Exposing the editor</h3>
To be used in SlickGrid an editor needs to be exposed globally under the window object, to do this you need to first create your editor constructors then extend them to the window.Slick.Editors object.

{% highlight javascript %}
(function ($) {
    /** Define editors first */
    function TextEditor() {}

    /** Create the Slick.Editors object if it does not exist */
    if (!window.Slick.Editors)
        window.Slick.Editors = {};

    /** Extend the Slick.Editors with ones you have defined */
    $.extend(true, window.Slick.Editors, {
        "Text": TextEditor
    });

})(jQuery);
{% endhighlight %}

<h3>Using a base constructor</h3>
Through experience I have found that using a constructor as a base and extending other editors from that is a great way to write editors for SlickGrid. Firstly you need to write your base editor, it needs to implement all of the required methods as prototypes that can be overwritten by individual editors. Firstly lets create the base editor constructor and extend Slick.Editors with it.

{% highlight javascript %}
/***
 * Base editor - From which all of our slickgrid editors are derived
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function BaseEditor() {}

    /** Create the Slick.Editors object if it does not exist */
    if (!window.Slick.Editors)
        window.Slick.Editors = {};

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Base": BaseEditor
    });
})(jQuery);
{% endhighlight %}

Now we have this in place we can set the prototypes for the BaseEditor constructor like this. I have not included the full methods for ease of reading, unless they are required, you can assume that it implements them in the same way as the basic editor above.

{% highlight javascript %}
/***
 * Base editor - From which all of our slickgrid editors are derived
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function BaseEditor() {}

    BaseEditor.prototype.init = function ()
    BaseEditor.prototype.destroy = function ()
    BaseEditor.prototype.focus = function ()
    BaseEditor.prototype.serializeValue = function ()
    BaseEditor.prototype.applyValue = function (item, state)
    BaseEditor.prototype.loadValue = function (item)
    BaseEditor.prototype.isValueChanged = function ()
    BaseEditor.prototype.validate = function ()

    /** Create the Slick.Editors object if it does not exist */
    if (!window.Slick.Editors)
        window.Slick.Editors = {};

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Base": BaseEditor
    });

})(jQuery);
{% endhighlight %}

The next step is to create our first editor, a simple text editor that will render an input field in the cell. When doing this we will extend the functionality of the BaseEditor constructor so we have a solid base to start from. Notice that as our BaseEditor implements a basic text editor we really don't need to be a lot, other than set arguments and run the init() method.

{% highlight javascript %}
/***
 * Text editor - Simple text editor for metadata
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function TextEditor (args) {
        this.args = args;
        this.init();
    }

    TextEditor.prototype = Slick.Editors.Base.prototype;

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Text": TextEditor
    });

})(jQuery);
{% endhighlight %}

<h3>More than an input</h3>
So really we want to do a little more than just show an input field, otherwise what is the point in using inheritance and prototyping to get the job done. Firstly we need to alter our init() prototype method, it needs to run a setup method if it is available, allowing us to do any extra initialisation we might want.

{% highlight javascript %}
BaseEditor.prototype.init = function () {
    if (this.args) {
        this.container  = this.args.container;
        this.column     = this.args.column;
        this.item       = this.args.item;
        this.grid       = this.args.grid;
    }

    this.$input = $('<input type="text" />')
        .on('keydown', this.handleKeyDown.bind(this));

    // Using underscore here helps with basic methods
    if (_.isFunction(this.setup))
        this.setup();

    this.$input.appendTo(this.container);

    this.focus();
};
{% endhighlight %}

We can now write something a little more useful, lets start with a date picker making use of the <a title="eternicode/bootstrap-datepicker" href="https://github.com/eternicode/bootstrap-datepicker" target="_blank">bootstrap datepicker plugin</a>. First we need to create the new constructor DatePickerEditor then specify our setup method, we also need to overwrite the destroy method as our datepicker will need to manually be destroyed.

{% highlight javascript %}
/***
 * DatePicker - A cell editor with bootstrap datepicker driven calendar
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function DatePicker (args) {
        var scope = this;
        this.args = args;

        this.setup = function () {
            this.$input.datepicker({
                    format: "dd/mm/yyyy",
                    todayHighlight: true,
                    forceParse: false
                })
                .on('changeDate', function () {
                    scope.$input.focus();
                });
        };

        this.destroy = function () {
            this.$input.datepicker('hide');
            this.$input.datepicker('destroy');
            this.$input.empty();
            this.$input.remove();
        };

        this.init();
    }

    DatePicker.prototype = Slick.Editors.Base.prototype;

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Date": DatePicker
    });

})(jQuery);
{% endhighlight %}

If you want to use this datepicker we would then set it in the column definition.

{% highlight javascript %}
{
    id          : "1",
    field       : "SortCode",
    name        : "Bank Sort Code *",
    validator   : "^[0-9]{6}$"
    editable    : true,
    editor      : SlickGrid.Editors.DatePicker,
    formatter   : SlickGrid.Formatters.TextFormatter
}
{% endhighlight %}

<h3>The finished product</h3>
And now the finished product, a base constructor for SlickGrid editors plus a DatePicker and Text editor.

__Slick.Editor.Base.js__
{% highlight javascript %}
/***
 * Base editor - From which all of our slickgrid editors are derived
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function BaseEditor() {}

    /**
     * init
     * Append the text field to the editor container and run setup if defined
     */
    BaseEditor.prototype.init = function () {
        if (this.args) {
            this.container  = this.args.container;
            this.column     = this.args.column;
            this.item       = this.args.item;
            this.grid       = this.args.grid;
        }

        this.$input = $('<input type="text" />');

        if (_.isFunction(this.setup)){
            this.setup();
        }

        this.$input.appendTo(this.container);

        this.focus();
    };

    /**
     * destroy
     * Remove the text field from the editor container
     */
    BaseEditor.prototype.destroy = function () {
        this.$input.empty();
        this.$input.remove();
    };

    /**
     * focus
     * Give focus to, and select the text of the editor field
     */
    BaseEditor.prototype.focus = function () {
        var scope = this;
        this.$input.focus();

        _.defer( function () {
            scope.$input.select();
        });
    };

    /**
     * serializeValue
     * Get the plain text contents of the editor field
     */
    BaseEditor.prototype.serializeValue = function () {
        return this.$input.val();
    };

    /**
     * applyValue
     * Apply the value of the editor field to the dataset in dataView
     */
    BaseEditor.prototype.applyValue = function (item, state) {
        item[this.column.field] = state;
    };

    /**
     * loadValue
     * Load the value of a cell in the dataset into the editor field
     */
    BaseEditor.prototype.loadValue = function (item) {
        var defaultValue = '';
        if (!_.isUndefined(item[this.column.field])) {
            defaultValue = item[this.column.field];
        }
        this.$input.val(defaultValue);
        this.$input.defaultValue = defaultValue;
        this.$input.select();
    };

    /**
     * isValueChanged
     * @return {boolean} - Has the data in the editor field changed when compared with the dataview
     */
    BaseEditor.prototype.isValueChanged = function () {
        return this.item !== this.$input.val();
    };

    /**
     * validate
     * @return {object}
     *      valid: is the input valid against the regex for that field
     *      msg: Failed validation message
     */
    BaseEditor.prototype.validate = function () {
        var validation = { valid: true };

        if (this.column.validator) {
            if (_.isNull(this.$input.val().match(new RegExp(this.column.validator)))) {
                validation.valid = false;
                validation.msg = "Failed validation: " + this.column.validatorMessage;
            }
        }

        return validation;
    };

    /** Create the Slick.Editors object if it does not exist */
    if (!window.Slick.Editors)
        window.Slick.Editors = {};

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Base": Base
    });

})(jQuery);
{% endhighlight %}

__Slick.Editor.DatePicker.js__
{% highlight javascript %}
/***
 * DatePicker - A cell editor with bootstrap datepicker driven calendar
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function DatePicker (args) {
        var scope = this;
        this.args = args;

        this.setup = function () {
            this.$input.datepicker({
                    format: "dd/mm/yyyy",
                    todayHighlight: true,
                    forceParse: false
                })
                .on('changeDate', function () {
                    scope.$input.focus();
                });
        };

        this.destroy = function () {
            this.$input.datepicker('hide');
            this.$input.datepicker('destroy');
            this.$input.empty();
            this.$input.remove();
        };

        this.init();
    }

    DatePicker.prototype = Slick.Editors.Base.prototype;

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Date": DatePicker
    });

})(jQuery);
{% endhighlight %}

__Slick.Editor.Text.js__
{% highlight javascript %}
/***
 * Text editor - Simple text editor for metadata
 * @module Editors
 * @namespace Slick
 */

(function ($) {
    function TextEditor (args) {
        this.args = args;
        this.init();
    }

    TextEditor.prototype = Slick.Editors.Base.prototype;

    /** Extend the Slick.Editors */
    $.extend(true, window.Slick.Editors, {
        "Text": TextEditor
    });

})(jQuery);
{% endhighlight %}

View the finished product on [Gist](https://gist.github.com/lgoldstien/f08185cd3b06ce7c55a9)
