The code examples on the documentation where a little messy. I have changed all code examples to follow this rules:

 - The code follows the Coding Standards, the [Symfony Coding Standards](http://symfony.com/doc/current/contributing/code/standards.html) as well as the [Twig Coding Standards](http://twig.sensiolabs.org/doc/coding_standards.html).
 - When we fold one or more lines of code we place `...` in a comment at the point where we fold some code. These comments are: `// ...` (php), `# ...` (yaml), `{# ... #}` (twig), `<!-- ... -->` (xml/html/php+html), `; ...` (ini), `...` (text)
 - When we fold a part of a line, e.g. a variable value, we put `...` (without comment) at the place of the fold.
 - Description about the fold:
   If we fold some lines: The description can be placed after the `...`
   If we fold a part of a line: The description can be placed before the line
 - If usefull, a codeblock begins with a comment with the filename. After this file comment should not be a blank line, except if the next line is a comment too

A very simple example:

    // src/Foo/Bar/foo.php
    function foo($bar) 
    {
        // set foo with a value of bar
        $foo = ...;

        // ... check if $bar has the correct value

        return $foo->baz($bar);
    }

 > **Points of attention**
 > - You should to put a space after `{` and before `}` in Yaml (`{ _controller: ... }`), but this should not be done in Twig (`{'hello' : 'value'}`).
 > - An array item is a piece of a line, not a total line. So don't use `// ...` but `...,` (the comma because of the Coding Standards):
       array(
           'some value',
           ...,
       )

Some rules that I haven't included, because it need some more discussion:

 - Do we use the sorthand `::` or the codeblock `.. code-block:: php` for php code?
 - Do we put a `$` before every bash line?

Because I have seen hunderts of lines it is sure I have missed some mistakes, so feel free to solve it or place a comment so I can solve it.
