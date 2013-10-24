## Styling and Layout

Styling defines several ready-to-use form layout styles. Those styles are defined inside CSS and are designed to give you best out-of-the-box experience with forms. In this section, I'm going to define my custom form class first and then I'm going to style it.

Forms support "stacked" class only starting from 4.2. Adding this class to a form puts variables above the labels. To style the form you should use regular grid system styling and assign it into fieldsets or fields themselves. For example if you assign "atk-row" column to the form, then use "span3" on your fieldset you get vertical form blocks.

If you assign atk-row class to fieldset and use span3 on field, you will get horizontal form blocks.

To set form class: `$form->setClass('atk-row')`.

The first field-set is already included in the form template, so to change that: `$form->template->trySet('fieldset', 'span4');`.

Subsequent field-sets can be set with `$form->addSeparator('span5');`.

Individual field classes are through `$field->setClass('...');`.