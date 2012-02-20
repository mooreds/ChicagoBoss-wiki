I took three functions (provided by Evan), enabled them, each one in the proper new tag section.
One in **html_tag**, one in **tag**, and one in **filter**.

The new directory structure looks like this:

![My Unicorn](http://thejeeper.net/static/images/filter_modules.png)

The **html_tag** is just this:
Filename: **src/view/lib/html_tags/select_tag.html**
Content:

    <select name="dummy" id="dummy">
      <option value="1">One</option>
      <option value="2">Two</option>
    </select>

The **tag** is this:
Filename: **src/view/lib/tag_modules/qbtimer_custom_tags.erl**
Content:

    -module(qbtimer_custom_tags).
    -compile(export_all).
    
    % put custom tags in here, e.g.
    %
    reverse(Variables, Options) ->
        error_logger:error_report( ["Variables:", Variables, "Options:", Options] ),
        lists:reverse(binary_to_list(proplists:get_value(string, Variables))).
    %
    % {% reverse string="hello" %} => "olleh"
    %
    % Variables are the passed-in vars in your template

and finally this is the **filter**:
Filename: **src/view/lib/filter_modules/qbtimer_custom_filters.erl**
Content:

    -module(qbtimer_custom_filters).
    -compile(export_all).
    
    % put custom filters in here, e.g.
    %
    my_reverse(Value) ->
         list_to_binary(lists:reverse(binary_to_list(Value))).
    %
    % "foo"|my_reverse   => "foo"

Now that I had all these pieces of code together, I wanted to see how they work:
Pick a simple view and paste these lines in …

    <table width="" cellspacing="0" cellpadding="4" border="1">
      <tr>
         <td>Filter:</td>
         <td>{{ "Hello world"|my_reverse }}</td>
         <td>&#123;&#123; "Hello world"|my_reverse  &#125;&#125;</td>
      </tr>
      <tr>
         <td>HTML_TAG</td>
         <td>{% select_tag %}</td>
         <td>&#123;&#37; select_tag &#37;&#125;</td>
      </tr>
      <tr>
         <td>Custom Tag</td>
         <td>{% reverse string="hello by custom tag" %}</td>
         <td>&#123;&#37; reverse string="hello by custom tag" &#37;&#125;</td>
      </tr>
    </table>

Now, start your project and enjoy the view :)
That might shed some light on the different ways of doing things in the UI.
