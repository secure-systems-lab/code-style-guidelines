# Python Guidelines

There are many excellent Python style guides out there, usually based on [PEP
8](https://www.python.org/dev/peps/pep-0008/) -- the mother of all Python style
guides. Following the principle of reuse, we recommend an existing guide that
we find suitable, and only add a few refinements and accents. In
short:

- Follow the recommendations of this document.
- Follow the recommendations of the [Google Python Style
  Guide](https://google.github.io/styleguide/pyguide.html) for everything that
  is not covered in this document.
- Follow local customs when working on existing code.
- Knowing [PEP 8](https://www.python.org/dev/peps/pep-0008/) doesn't hurt.
- Strive for readability and keep junior programmers in mind.
- Though maintainers and linters have the authority, you may challenge them if you
  have good reason to do so.
- Add a suitable linter configuration to your project if it does not have one.
  You can take inspiration from well-established Python lab projects such as
  [in-toto](https://github.com/in-toto/in-toto) and
  [TUF](https://github.com/theupdateframework/tuf).
- If you are familiar with auto-formatters such as `yapf`, `black`, `autopep8`,
  etc. feel free to share a config file that adheres to this guidelines
  document. :)


### Indentation and Line Continuation
*[cf. Google Python Style Guide [Line
length](https://google.github.io/styleguide/pyguide.html#32-line-length) and
[Indentation](https://google.github.io/styleguide/pyguide.html#34-indentation)]*

Use **4 spaces** per indentation level for new projects, but favor consistency
in existing projects (most of them use 2 spaces).

Favor **aligned indentation** over hanging indentation for line continuation
and resolve potential problems like so:

- **Problem:** Long first lines push continued lines to the right edge:

  ```python
  # NO:
  import package.has.many.sub.packages.or_long_package_names.foo

  # This problem occurs when picking long function names ...
  def this_function_has_a_very_long_name_and_many_arguments(lorem, ipsum_dolor,
                                                            sit, amet,
                                                            consectetur):
    # ... or as result of 'import <full module path>' syntax.
    package.has.many.sub.packages.or_long_package_names.foo.bar(lorem,
                                                                ipsum_dolor,
                                                                sit, amet,
                                                                consectetur)
  ```

  **Solution:** Avoid long function names and use `from <full package path>
  import <module>`- syntax:

  ```python
  # YES:
  from package.has.many.sub.packages.or_long_package_names import foo

  def call_foo_bar(lorem, ipsum_dolor, sit, amet, consectetur):
    foo.bar(lorem, ipsum_dolor, sit, amet, consectetur)
  ```

- **Problem:** Indentation of opening parenthesis might coincide with logical
  indentation of nested block:

  ```python
  # NO:
  # As a matter of fact, it will always coincide for 'if' statements
  if (lorem and ipsum_dolor and sit and amet and consectetur and
      adipisicing and elit):
      foo()
  ```

  **Solution:** Start nested block with a comment to add visual distinction:

  ```python
  # YES:
  if (lorem and ipsum_dolor and sit and amet and consectetur and
      adipisicing and elit):
      # TODO: Add meaningful comment about 'foo()' and its conditions
      foo()
  ```
  Note that function definitions may also deindent the closing parenthesis to
  align with the `def` keyword, as demonstrated in the Google Python Style Guide
  [Type
  Annotations](https://google.github.io/styleguide/pyguide.html#3192-line-breaking)
  section.

- **Problem:** Renames in first line might require re-aligning continued lines.\
  **Solution:** Avoid renames by picking [good
  names](https://duckduckgo.com/?q=There+are+only+two+hard+things+in+Computer+Science%3A+cache+invalidation+and+naming+things)
  from the start. :P

- **Special case:** Also align continued lines of continued lines:

  ```python

  # YES:
  raise Exception("Lorem {0} ipsum {1} dolor sit amet, {2} consectetur {3} ad "
                  "{4} rcitatio {5}.".format(reprehenderit, voluptate, velit,
                                             pariatur, deserunt, laborum))
  ```

### Code Documentation
*[cf. Google Python Style Guide [Comments and
Docstrings](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings)]*

#### General Recommendations

- Write docstrings and comments before or as you write your code. You
  will never again understand your code better.
- Don't state the obvious! Don't spam! If someone else (including the future
  you) thinks it is less effort to decipher your code than to read its
  documentation, then you probably commented too much. Plus, more docs means
  more maintenance work.
- Code documentation that contradicts the code is worse than no code
  documentation. Always make a priority of keeping the docstrings and comments
  up-to-date when the code changes.

#### Docstrings

- Add at least the docstring sections described in the [Google Python Style
  Guide](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings).
- Add a lab-specific `Side Effects` section, for any side
  effects of your function. If you have doubts about whether your function has
  side effects, consult with the reviewer.
- Add other sections supported by the [Napoleon Sphinx
  extension](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/#docstring-sections)
  as you see fit.
- `Returns` should be the last section.
- Don't add empty sections.
- Generally try to minimize vertical space by avoiding redundancy and
  aiming for conciseness. It is desirable to fit docstring and function
  body on a reasonably sized screen.
- Use vanilla
  [reStructuredText/Sphinx](https://www.sphinx-doc.org/en/master/usage/restructuredtext/index.html)
  markup sparingly in order to enhance visual appeal on [*Read the
  Docs*](https://docs.readthedocs.io/en/stable/), but without impeding
  readability of raw docstrings. For instance, it is a good idea to mark up a
  block of code using the rather subtle [double colon, blank line and
  indentation](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html#literal-blocks),
  but not to litter a docstring with [Sphinx
  directives](https://www.sphinx-doc.org/en/master/usage/restructuredtext/directives.html).


#### TODO-comments
*[cf. Google Python Style Guide [TODO Comments](
https://google.github.io/styleguide/pyguide.html#312-todo-comments)]*

Adding a parenthesized identifier in a `# TODO:`-comment is not necessary. The
version control system can provide that information just as well.

### Strings
*[cf. Google Python Style Guide
[Strings](https://google.github.io/styleguide/pyguide.html#310-strings)]*

#### Quotes
Favor double quotes `"` over single quotes `'` for regular strings, unless the
string itself contains double quotes. If the string contains both single and
double quotes, pick whatever requires less escaping inside the string.

#### String Formatting
Use `f"...{var}"` when writing Python 3.6+ code, or `"...{0}".format(var)`
anywhere. `"..." + var` may be used for simple cases, but beware of pitfalls
such as easily missed whitespace, or `var` not being a string. Don't use the
old `"...%s" % var`-notation.
