# User Interface > Template Engine (SMLite)
----

This page covers some of the advanced topics of SMLite. For usage and introduction, follow to [introduction to templates](template-engine/introduction-to-templates.md "introduction to templates")

## Parsing
When template is loaded, it's represented in the memory as an array. Example Template:

    Hello <?subject?>world<?/?>!!
SMLite converts the template into the following structure available under `$smlite->template`.

Content of tags are parsed recursively and will contain further arrays. In addition to the template tree, tags are indexed and stored inside "tags" property.

    // template property:
    array ( 
      0 => 'Hello ', 
      'subject#1' => array ( 
        0 => 'world', 
      ), 
      1 => '!!', 
    )

    // tags property
    array (
      'subject'=> array( &array ),
      'subject#1'=> array( &array )
    )
As a result each tag will actually add two tags. If tag with same name is added, reference to a region is added inside respective tag sub-array. This allow `$smlite->get()` to quickly retrieve contents of appropriate tag and it will also allow `render()` to reconstruct the output.