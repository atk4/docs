# Agile CSS Framework

Agile Toolkit relies on HTML and CSS for positioning and presentation of User Interface. The goal of CSS framework is to disappear from the "TODO" list of our new developers who are not yet focused on look and feel an simply do a good job of making everything look pretty and professional. The second goal of the framework is to "be there" for those who are developing and designing a more complex layouts


## Table of Contents
 
 - Features of Agile Framework
 - Introduction to Widgets and Components
 - Layout system
 - Grid system
 - Color schemes and palettes
 - Typography and font sizes
 - Media blocks
 - Shapes and Masks
 - Paddings and Spacing
 - Icons
 - jQuery UI theme
 - Standard Widgets
   - Navigation
   - Item Lister
   - Labels
   - Forms
   - Tables and Grids
   - Icons 
 - Advanced topics

 
## Features of Agile Toolkit
 
Agile Toolkit is based around the concepts of re-usable classes. This pattern is more in-line with the jQuery UI CSS framework.

### Components and Widgets

For example a class `atk-effect-danger` is defined in a universal way allowing you to apply it on buttons, labels, table rows, media elements and many other UI elements. Another class `atk-box` creates paddings for a block.

Instead of designing large variety of purpose-oriented class - Agile Toolkit combines a number of standard classes to achieve required result.

We refer to those re-usable classes as **Components**. When a part of the UI is built out of those components, such as top-bar with user menu, we refer to that piece of the HTML code as a **Widget**.

This approach contributes greatly to the flexibility of our UI library and makes it easy for third party to develop additional elements.

Agile Toolkit is also backwards compatible with jQuery UI theme, however we cleaned it up and make it more appealing for modern design guidelines.

### Important Notes

Agile Toolkit stylesheets does never attempt to modify existing element look and feel. The exception are normalize.less and typography. Agile Toolkit stylesheets can be easily combined with other CSS frameworks and will cause no conflicts.

Agile Toolkit also adjusts default jQuery UI CSS styles to look more modern with use of CSS3


## Layout System

Traditionally a "Grid Systems" have been using for managing overall page layout. This is true for CSS frameworks such as Bootstrap and was also used in versions up to 4.2 of Agile Toolkit.  Unfortunately using "Grid System" to arrange your page content is rather limiting  and restricting practice.

A new concept is introduced called "Layouts". It works only with CSS3-supporting browsers but allows a much higher control of your interface.

Layout can also perform much better in regards to responsive user interface. The different panels of the layout will re-orient themselves properly any need for developer or designer to worry about it.

## Responsive Grid system

We like the simplicity of a good grid system. Agile Toolkit comes bundled with 12-column flexible grid system. That means you can use grids within any container and they will always consume full width.

The sizing classes (span_5, span_2 etc) can also be used outside of grid rows and can be applied to practically any element to set a proper relative size to it. This is extremely valuable in layout set-up - allows you to set a relative size of your sliding-out sidebars.

Grid system columns will automatically re-orient themselves when the space is limited.

    TODO - more info. Link to responsive grid source ..
    also information on how to switch to a different grid system.
    
    

## Color Schemes and Palettes

Agile Toolkit CSS framework contains two sets of color palettes. The first one is standard and consisting of colors which generally wouldn't be changed. The color are associated with severity of different states. The class names go like this:

 - atk-effect-info, atk-effect-danger, atk-effect-success, etc.
 
There are few more colors which are not fixed to the colors and might change depending on the theme / palette you are using.

    TODO - specify proper prefixes
    
This color palette is also applied to the jQuery UI elements to make them look consistently with the rest of the interface.  The basic concept behind the standardized color palette is to allow developers of certain add-ons to use the variety of default theme colors without introducing new, inconsistent colors.


## Typography and Text Sizes
Agile Toolkit comes with a correct typography and paragraph paddings. Any text you put on your page with default paragraph styles will look properly sized and will use consistent fonts. 

Toolkit also introduces a way to globally affect font sizes. For example if you want content of the table to look smaller you can apply a class `atk-font-mini` which will make the contents smaller. This adjustments of fonts will also affect the em-based paddings. This feature can be used to consistently resize elements without screwing up proportions.

## Icons
Agile Toolkit switched to use the font-based icons. You should follow documentation of fontello / `TODO link`. The benefit of using font-based icon sets are huge - they are always properly aligned to the text, they maintain same size and color, they are vector based and easy to use.

To keep the size of standard framework distribution relatively compact, agile toolkit includes only basic set of icons. You can add additional icons either ..... or by using our super-addon. 

## Media Blocks, Paddings and Spaces
A set of containers which is designed to emphasize presentation of information on your page are called "Media Blocks". Those are different types of boxes, frames and wrappers to make content more appealing. Here is a list of all media elements:

 - atk-box - Emphasize some information by adding a background color and paddings. Can be combined with coloring classes.
 - atk-label - Add this to your text data to indicate status, problem serenity or tag.
 - atk-avatar - A frame for displaying profile pictures or portraits. Can be combined with atk-rounded or atk-circled classes


## Helper classes

 - atk-jackscrew - expand element full width
 - atk-right, atk-left, atk-center - align text
 - 
 
 
## Tables

 - Agile Toolkit does not alter default look and feel of the tables, but it does offer you a class for making tables look more neat. Simply add atk-table to the table or some of it's parents. 

ok 

    NOTE: atk-table will affect all contained tables,
    in case we want to nest them. is that good? I guess that's ok.
     
     
 
## Standard Widgets

    TODO

 - Navigation: 
 - 