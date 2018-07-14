# Code Style Guidelines #

These guidelines provide examples of what to do (or not to do) when writing
C code for the projects of the [Secure Systems Lab](https://ssl.engineering.nyu.edu/)
in the NYU Tandon School of Engineering.

These guidelines are based on a document describing [the preferred coding style](https://www.kernel.org/doc/html/v4.10/process/coding-style.html)
for the Linux kernel. As the document states:

> "Coding style is very personal, and I won't **force** my views on anybody
> but this is what goes for anything that I have to be able to maintain, and
> I'd prefer it for most other things too. Please at least consider the points
> made here."

## Table of Contents ##
- [Introduction](#introduction)
- [Indentation](#indentation)
- [Breaking long lines and strings](#long-lines)
- [Placing braces and spaces](#braces-and-spaces)
- [Spaces](#spaces)
- [Naming](#naming)
- [Typedefs](#typedefs)
- [Functions](#functions)
- [Centralized exiting of functions](#function-exit)
- [Commenting](#commenting)
- [Macros](#macros)
- [Function return values and names](#function-return-and-names)
- [Conditional Compilation](#conditional-compilation)
- [References](#refernces)

<a name="introduction"/></a>
# Introduction

You might think the main point of having a consistent coding style is to
have a legible, easy-to-mentally-parse template which everyone follows, and
yes, this is definitely one of the more useful side-effects, but it isn't most
important reason by far.

Humans are quite effective at recognizing patterns; it's one of the main
reasons why we find it so easy to do things which are excruciatingly difficult
for computers (all those exhilarating CAPTCHAs where you are tasked with
finding all the pictures containing cars comes to mind), and this is just
as true when it comes to reading computer code; both our own as well as
code written by others.

When reading code with consistent style, its repetitiveness tricks you into
tuning out the style completely: your brain no longer considers it important
important to bother caring about and you begin to notice strange code structure
or missing semantics. When you stop unconsciously judging code by how much you
like or dislike its style you naturally start judging code by its **worth
instead**. Mistakes start to jump out at you, and you begin to see useful,
reusable solutions that you had previously overlooked.

So please keep that in mind while reading this document; there is a higher
purpose to all of this, I promise.

Finally, remember that these are all **guidelines** and not unbreakable
rules. They are meant to help, not oppress, so if breaking a rule or
two keeps your code from becoming a jumbled mess, don't hesitate to do
so. It's almost certainly possible for single 90 character conditional
to be easier read than a shorter, but multi-line, one. There will **always**
exceptions like this for the guidelines which follow, and we are all
intelligent adults here who can judgment calls when required, so never
be afraid to exercise common sense.

Nonetheless, if there are any guidelines here which you absolutely cannot
stand, or if there are any grave pragmatic or security concerns regarding any
of these guidelines, please don't hesitate to direct any feedback to
@alyptik (Joey Pabalinas <joeypabalinas@gmail.com>).

About half of the document is verbatim from the Linux kernel coding style
guidelines (of which I am a staunch believer in, as you can probably
tell), but there were guidelines which I felt need to be modified to better
reflect the differences in goals and project types worked on by the NYU Tandon
School of Engineering.

<a name="indentation"/></a>
# 1\) Indentation

Tabs are 8 characters, and thus indentations are also 8 characters.
There are heretic movements that try to make indentations 4 (or even
2\!) characters deep, and that is akin to trying to define the value of
pi to be 3.

**Rationale:** The whole idea behind indentation is to clearly define where
a block of control starts and ends. Especially when you've been looking
at your screen for 20 straight hours, you'll find it a lot easier to see
how the indentation works if you have large indentations.

Now, some people will claim that having 8-character indentations makes
the code move too far to the right, and makes it hard to read on a
80-character terminal screen. The answer to that is that if you need
more than 3 levels of indentation, you're screwed anyway, and should fix
your program.

In short, 8 character indents make things easier to read, and have the
added benefit of warning you when you're nesting your code blocks too deep.

Heed that warning.

The preferred way to ease multiple indentation levels in a switch
statement is to align the `switch` and its subordinate `case` labels in
the same column instead of `double-indenting` the `case` labels.

``` c
/* good */
switch (suffix) {
case 'G':
case 'g':
	mem <<= 30;
	break;
case 'M':
case 'm':
	mem <<= 20;
	break;
case 'K':
case 'k':
	mem <<= 10;
	/* fall through */
default:
	break;
}
```

Don't put multiple statements on a single line unless you have something
to hide:

``` c
/* BAD */
if (condition) do_this;
 do_something_everytime;
```

Don't put multiple assignments on a single line either. Avoid tricky
expressions. Keep your coding style simple, stupid™.

Don't leave whitespace at the end of lines; they can make applying those
diffs more, well, difficult, and most editors these days have features
to quickly remove all trailing whitespace from a document.

Outside of comments and documentation, spaces should never be used for
indentation, and the above example is deliberately broken. It's much
easier to avoid mixed and incorrect indentation with tabs, as well as
to modify or remove indentation levels.

<a name="long-lines"/></a>
# 2\) Breaking long lines and strings

Coding style is all about readability and maintainability using commonly
available tools.

The limit on the length of lines is 80 columns and this is a strongly
preferred limit.

Statements longer than 80 columns should be broken into sensible chunks,
unless exceeding 80 columns significantly increases readability and does
not hide information. If this happens to be the case, descendants should
always be substantially shorter than the parent and should be placed
substantially to the right. The same applies to function headers with
a long argument list. However, never break user-visible strings such as
`puts()` or `printf()` messages, because that breaks the ability to grep for
them.

<a name="braces-and-spaces"/></a>
# 3\) Placing braces and spaces

The other issue that always comes up in C styling is the placement of
braces. Unlike the indent size, there are few technical reasons to
choose one placement strategy over the other, but the preferred way, as
shown to us by the prophets Kernighan and Ritchie, is to put the opening
brace last on the line, and put the closing brace first, thusly:

``` c
/* good */
if (x_is_true) {
	we_do_y();
}
```

This applies to all non-function statement blocks (`if`, `switch`, `for`,
`while`, `do`).

``` c
/* good */
switch (action) {
case KOBJ_ADD:
	return "add";
case KOBJ_REMOVE:
	return "remove";
case KOBJ_CHANGE:
	return "change";
default:
	return NULL;
}
```

However, there is one special case, namely functions: they have the
opening brace at the beginning of the next line, thus:

``` c
/* good */
int function(int x)
{
	body of function
}
```

Functions are special; you can't nest them in standard C.

Note that the closing brace is empty on a line of its own, **except** in
the cases where it is followed by a continuation of the same statement,
i.e. a `while` in a do-statement or an `else` in an if-statement, like
this:

``` c
/* good */
do {
	body_of_do_loop();
} while (condition);
```

and

``` c
/* good */
if (x == y) {
	foo();
} else if (x > y) {
	bar();
} else {
	baz();
}
```

**Rationale:** K\&R2.

It's also useful to note that this brace-placement minimizes the number of
empty (or almost empty) lines, without any loss of readability. Thus, as the
supply of newlines on your screen is not a renewable resource (think 25-line
terminal screens here), you have more empty lines to put comments on.

Use braces even for single statements:

``` c
/* good */
if (condition) {
	action();
}
```

and

``` c
/* good */
if (condition) {
	do_this();
} else {
	do_that();
}
```

**Rationale:** Everyone makes mistakes, but some mistakes, as shown by Apple's ["goto fail"](https://nakedsecurity.sophos.com/2014/02/24/anatomy-of-a-goto-fail-apples-ssl-bug-explained-plus-an-unofficial-patch/):

``` c
/* BAD BAD BAD */
hashOut.data = hashes + SSL_MD5_DIGEST_LEN;
hashOut.length = SSL_SHA1_DIGEST_LEN;
if ((err = SSLFreeBuffer(&hashCtx)) != 0)
    goto fail;
if ((err = ReadyHash(&SSLHashSHA1, &hashCtx)) != 0)
    goto fail;
if ((err = SSLHashSHA1.update(&hashCtx, &clientRandom)) != 0)
    goto fail;
if ((err = SSLHashSHA1.update(&hashCtx, &serverRandom)) != 0)
    goto fail;
if ((err = SSLHashSHA1.update(&hashCtx, &signedParams)) != 0)
    goto fail;
    goto fail;  /* MISTAKE! THIS LINE SHOULD NOT BE HERE */
if ((err = SSLHashSHA1.final(&hashCtx, &hashOut)) != 0)
    goto fail;

err = sslRawVerify(…);
```

are very easily prevented.

It's **always** better to code defensively, no matter how careful you **think** you are.

<a name="spaces"/></a>
## 3.1) Spaces

The use of spaces depends (mostly) on function-versus-keyword usage. Use a
space after (most) keywords. The notable exceptions are `sizeof()`, `typeof()`,
`alignof()`, and `__attribute__()`, when using the form resembling functions.

So use a space after these keywords:

> if, switch, case, for, do, while

but not with the function-resembling forms of `sizeof()`, `typeof()`,
`alignof()`, and `__attribute__()`.

``` c
/* good */
s = sizeof(struct file);
```

Do not add spaces around (inside) parenthesized expressions. This
example is **bad**:

``` c
/* BAD */
s = sizeof( struct file );
```

When declaring pointer data or a function that returns a pointer type,
the preferred use of `*` is adjacent to the data name or function name
and not adjacent to the type name. Examples:

``` c
/* good */
char *linux_banner;
unsigned long long memparse(char *ptr, char **retptr);
char *match_strdup(substring_t *s);
```

Use one space around (on each side of) most binary and ternary
operators, such as any of these:

> =  +  -  <  >  *  /  %  |  &  ^  <=  >=  ==  !=  ?  :

but no space after unary operators:

> &  *  +  -  ~  !  `sizeof()`  `typeof()`  `alignof()`  `__attribute__()`  `defined()`

no space before the postfix increment & decrement unary operators:

> ++  --

no space after the prefix increment & decrement unary operators:

> ++  --

and no space around the `.` and `->` structure member operators.

Do not leave trailing whitespace at the ends of lines. Some editors with
`smart` indentation will insert whitespace at the beginning of new lines
as appropriate, so you can start typing the next line of code right
away. However, some such editors do not remove the whitespace if you end
up not putting a line of code there, such as if you leave a blank line.
As a result, you end up with lines containing trailing whitespace.

Git will warn you about patches that introduce trailing whitespace, and
can optionally strip the trailing whitespace for you; however, if
applying a series of patches, this may make later patches in the series
fail by changing their context lines.

<a name="naming"/></a>
# 4\) Naming

C is a Spartan language, and so should your naming be. Unlike Modula-2
and Pascal programmers, C programmers do not use cute names like
`ThisVariableIsATemporaryCounter`. A C programmer would call that variable
`cnt`, which is much easier to write, and not the least more difficult
to understand.

HOWEVER, while mixed-case names are frowned upon, descriptive names for
file-scope identifiers are a must. To call an externally visible function
`f()` is a shooting offense.

**FILE-SCOPE** variables (to be used only if you **really** need them) need to
have descriptive names, as do functions. If you have a function that counts
the number of active users, you should call that `count_active_users()` or
similar, you should **not** call it `cntusr()`.

Encoding the type of a function into the name (so-called Hungarian
notation) is pointless - the compiler knows the types anyway and can
check those, so it only serves to confuse the programmer.

**LOCAL** variable names should be short, and to the point. If you have some
random integer loop counter, it should probably be called `i`. Calling
it `loop_counter` is non-productive if there is no chance of it being
misunderstood. Similarly, `tmp` can be just about any type of variable
that is used to hold a temporary value.

If you are afraid to mix up your local variable names, you have another
problem, which is called the function-growth-hormone-imbalance syndrome.
See section 6 ([Functions](#functions)).

<a name="typedefs"/></a>
# 5\) Typedefs

Please don't use things like `vps_t`. It's a **mistake** to use `typedef`
for non-opaque structures and pointers. When you see a

``` c
/* BAD */
vps_t a;
```

in the source, what does it mean? Is it a `struct`? A `union`? A function
pointer? In contrast, if it says

``` c
/* good */
struct virtual_container *a;
```

you can actually tell what `a` is.

Lots of people think that typedefs help readability. Not so. They are
useful only for:

	a) Totally opaque objects (where the typedef is actively used to
	   *_hide_ what the object is).

	   Example: `pte_t` etc. opaque objects that you can only access
	   using the proper accessor functions.

	   Opaqueness and `accessor functions` are not good in themselves.
	   The reason we have them for things like `pte_t` etc. is that there
	   really is absolutely _zero_ portably accessible information
	   there.

	b) Clear integer types, where the abstraction _helps_ avoid
	   confusion whether it is `int` or `long`.

	   `u8`/`u16`/`u32` are perfectly fine typedefs, although they fit into
	   category (c) better than here.

	   Again - there needs to be a reason for this. If something is
	   `unsigned long`, then there's no reason to typedef,
	   but if under certain circumstances it might be an `unsigned int`
	   and under other configurations might be `unsigned long`, then
	   by all means go ahead and use a typedef.

	c) New types which are identical to standard C99 types, in certain
	   exceptional circumstances.

	   Although it would only take a short amount of time for the eyes
	   and brain to become accustomed to the standard types like
	   `uint32_t`, some people object to their use anyway.

	   Therefore, the Linux-specific `u8/u16/u32/u64` types and their
	   signed equivalents which are identical to standard types are
	   permitted - although they are not mandatory in new code of your
	   own.

	   When editing existing code which already uses one or the other set
	   of types, you should conform to the existing choices in that code.

Maybe there are other cases too, but the rule should basically be to
NEVER EVER use a typedef unless you can clearly match one of those
rules.

In general, a pointer, or a struct that has elements that can reasonably
be directly accessed should **never** be a typedef.

<a name="functions"/></a>
# 6\) Functions

Functions should be short and sweet, and do just one thing. They should
fit on one or two screenfuls of text (the ISO/ANSI screen size is 80x24,
as we all know), and do one thing and do that well.

The maximum length of a function is inversely proportional to the
complexity and indentation level of that function. So, if you have a
conceptually simple function that is just one long (but simple)
case-statement, where you have to do lots of small things for a lot of
different cases, it's OK to have a longer function.

However, if you have a complex function, and you suspect that a
less-than-gifted first-year high-school student might not even
understand what the function is all about, you should adhere to the
maximum limits all the more closely. Use helper functions with
descriptive names (you can ask the compiler to `inline` them if you think
it's performance-critical, and it will probably do a better job of it
than you would have done).

Another measure of the function is the number of local variables. They
shouldn't exceed 5-10, or you're doing something wrong. Re-think the
function, and split it into smaller pieces. A human brain in general can
easily keep track of about 7 different things, but anything more and it
gets confused. You know you're brilliant, but maybe you'd like to
understand what you did 2 weeks from now.

In source files, separate functions with one blank line.

```c
/* good */
int system_is_up(void)
{
	return system_state == SYSTEM_RUNNING;
}

int system_is_dowm(void)
{
	return system_state == SYSTEM_DOWN;
}
```

In function prototypes, include parameter names with their data types.
Although this is not required by the C language, it is preferred because
it is a simple way to add valuable information for the reader.

<a name="function-exit"/></a>
# 7\) Centralized exiting of functions

Albeit deprecated by some people, the equivalent of the `goto` statement
is used frequently by compilers in form of the unconditional jump
instruction.

The `goto` statement comes in handy when a function exits from multiple
locations and some common work such as cleanup has to be done. If there
is no cleanup needed then just return directly.

Choose label names which say what the goto does or why the goto exists.
An example of a good name could be `out_free_buffer:` if the goto frees
`buffer`. Avoid using GW-BASIC names like `err1:` and `err2:`, as you
would have to renumber them if you ever add or remove exit paths, and
they make correctness difficult to verify anyway.

The rationale for using gotos is:

	- Unconditional statements are easier to understand and follow.
	- Nesting is reduced.
	- Errors by not updating individual exit points when making
	  modifications are prevented.
	- Saves the compiler work to optimize redundant code away.

So use them in situations like this to avoid making already confusing code
even more complicated:

``` c
/* good */
int fun(int a)
{
	int result = 0;
	char *buffer;

	buffer = kmalloc(SIZE, GFP_KERNEL);
	if (!buffer) {
		return -ENOMEM;
	}

	do_stuff();
	if (condition) {
		result = 1;
		kfree(buffer);
		while (other_condition) {
			do_other_stuff();
		}
		goto out_no_free;
	}

	if (!do_even_more_stuff()) {
		goto out_free_buffer;
	}

	for (;;) {
		if (loop_predicate()) {
			break;
		}
	}

	result = 2;
out_free_buffer:
	kfree(buffer);
out_no_free:
	return result;
}
```

A common type of bug to be aware of are "one `err` bugs" which look like
this:

``` c
/* BAD */
err:
	/* foo might be NULL */
	kfree(foo->bar);
	kfree(foo);
	return ret;
```

The bug in this code is that on some exit paths `foo` is NULL. Normally
the fix for this is to split it up into two error labels `err_free_bar:`
and `err_free_foo:`:

``` c
/* good */
err_free_bar:
	kfree(foo->bar);
err_free_foo:
	kfree(foo);
	return ret;
```

Ideally you should simulate errors to test all exit paths.

<a name="commenting"/></a>
# 8\) Commenting

Comments are good, but there is also a danger of over-commenting. NEVER
try to explain HOW your code works in a comment: it's much better to
write the code so that the **working** is obvious, and it's a waste of
time to explain badly written code.

Generally, you want your comments to tell WHAT your code does, not HOW.
Also, try to avoid putting comments inside a function body: if the
function is so complex that you need to separately comment parts of it,
you should probably go back to chapter 6 for a while. You can make small
comments to note or warn about something particularly clever (or ugly),
but try to avoid excess. Instead, put the comments at the head of the
function, telling people what it does, and possibly WHY it does it.

The preferred style for long (multi-line) comments is:

``` c
/*
 * This is the preferred style for multi-line
 * comments in your source code. Please use
 * it consistently.
 *
 * Description:  A column of asterisks on the left side,
 * with beginning and ending almost-blank lines.
 */
```

It's also important to comment data, whether they are basic types or
derived types.

<a name="macros"/></a>
# 9\) Macros

Names of macros defining constants and labels in enums are capitalized.

``` c
#define CONSTANT 0x12345
```

Enums are preferred when defining several related constants.

**CAPITALIZED** macro names are appreciated but macros resembling functions
may be named in lower case. Generally, inline functions are preferable to
macros resembling functions.

Macros with multiple statements should be enclosed in a do - while block
to allow it for a semicolon when used (so that `macrofun(x, y, z);`
resembles a function call):

```c
/* good */
#define macrofun(a, b, c)			\
	do {					\
		if ((a) == 5) {			\
			do_this((b), (c));	\
		}				\
	} while (0)
```

##### Things to avoid when using macros:

a\) Macros that affect control flow:

```c
/* good */
#define FOO(x)					\
	do {					\
		int i = (x) & FATAL_SIG_MASK;	\
		if (blah(i) < 0) {		\
			return -EBUGGERED;	\
		}				\
	} while (0)
```

are a **very** bad idea. It looks like a function call but exits the
**calling** function; don't break the mental parsers of those who will
read the code.

b\) Macros that depend on having a local variable with a magic name:

```c
/* BAD */
#define FOO(val) bar(magic_index, val)
```

might look like a good thing, but it's confusing as hell when one reads
the code and it's prone to breakage from seemingly innocent changes.

c\) Macros with arguments that are used as lvalues:

```c
/* BAD */
FOO(x) = y;
```

**will** bite you if somebody e.g. turns `FOO()` into an inline function.

d\) Forgetting about precedence: macros defining constants using
expressions must enclose the expression in parentheses. Beware of
similar issues with macros using parameters.

```c
/* good */
#define CONSTANT 0x4000
#define CONSTEXP (SOME_CONSTANT|OTHER_CONSTANT)
```

e\) Namespace collisions when defining local variables in macros
resembling functions (such as GNU statement expressions):

```c
/* BAD */
#define FOO(x)			\
({				\
	typeof(x) ret;		\
	ret = calc_ret(x);	\
	(ret);			\
})
```

`ret` is a common name for a local variable - `__foo_ret` is less likely
to collide with an existing variable.

<a name="function-return-and-names"/></a>
# 10\) Function return values and names

Functions can return values of many different kinds, and one of the most
common is a value indicating whether the function succeeded or failed.
Such a value can be represented as an error-code integer (-Exxx =
failure, 0 = success) or a `succeeded` boolean (0 = failure, non-zero =
success).

Mixing up these two sorts of representations is a fertile source of
difficult-to-find bugs. If the C language included a strong distinction
between integers and booleans then the compiler would find these
mistakes for us… but it doesn't. To help prevent such bugs, always
follow this convention:

> If the name of a function is an action or an imperative command,
> the function should return an error-code integer.  If the name
> is a predicate, the function should return a "succeeded" boolean.

For example, "add work" is a command, and the `add_work()` function
returns 0 for success or `-EBUSY` for failure. In the same way, "PCI
device present" is a predicate, and the `pci_dev_present()` function
returns 1 if it succeeds in finding a matching device or 0 if it
doesn't.

Functions whose return value is the actual result of a computation,
rather than an indication of whether the computation succeeded, are not
subject to this rule. Generally they indicate failure by returning some
out-of-range result. Typical examples would be functions that return
pointers; they use `NULL` to report failure.

<a name="conditional-compilation"/></a>
# 11\) Conditional Compilation

Wherever possible, don't use preprocessor conditionals (`#if`, `#ifdef`)
in .c files; doing so makes code harder to read and logic harder to
follow. Instead, use such conditionals in a header file defining
functions for use in those .c files, providing no-op stub versions in
the \#else case, and then call those functions unconditionally from .c
files. The compiler will avoid generating any code for the stub calls,
producing identical results, but the logic will remain easy to follow.

Prefer to compile out entire functions, rather than portions of
functions or portions of expressions. Rather than putting an `#ifdef` in an
expression, factor out part or all of the expression into a separate
helper function and apply the conditional to that function.

At the end of any non-trivial `#if` or `#ifdef` block (more than a few
lines), place a comment after the `#endif` on the same line, noting the
conditional expression used. For instance:

``` c
/* good */
#ifdef CONFIG_SOMETHING
…
#endif /* CONFIG_SOMETHING */
```
<a name="references"/></a>
# Appendix I) References

The C Programming Language, Second Edition by Brian W. Kernighan and
Dennis M. Ritchie. Prentice Hall, Inc., 1988. ISBN 0-13-110362-8
(paperback), 0-13-110370-9 (hardback).

The Practice of Programming by Brian W. Kernighan and Rob Pike.
Addison-Wesley, Inc., 1999. ISBN 0-201-61586-X.

WG14 is the international standardization working group for the
programming language C, URL: <http://www.open-std.org/JTC1/SC22/WG14/>

Kernel process/coding-style.rst, by <greg@kroah.com> at OLS 2002:
<http://www.kroah.com/linux/talks/ols_2002_kernel_codingstyle_talk/html/>
