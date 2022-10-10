# Documentation template

## Page contents

### Page title

Always put a page title at the top, preferably using the same wording as in the left side navigation menu, or longer (more precise) if you had to shorten the menu title. This quickly brings us to:

### Menu title

### Headers

The documentation uses four levels of Headers. Note the space after the hash-signs. The headers used in the document are also used to create the structure shown to the right of the text.

=== "First level header"

    This is only used for the Page title

    ```
    # First level header
    ```

=== "Second level header"

    Used for main sections of a page.

    ```
    ## Second level header
    ```

=== "Third level header"

    ```
    ### Third level header
    ```

=== "Fourth level header"

    ```
    #### Fourth level header
    ```

If you feel that you need more subheaders than four, try to find a different way to layout your page, using e.g. a tabbed section like the one above.

### Text formatting

Text can be **bolded**, *italicized*, or ***both***.

```
    Text can be **bolded**, *italicized*, or ***both***.
```

__Underlining__ text is accomplished using:



### Links

### Colors

### Pictures

#### Stock images

#### Screenshots

Try to keep screenshots as clean as possible, only showing the windows or sections that are described in the text.

Avoid using actual email adresses, staff names etc in screenshots. Apart from the risk of receiving unwanted emails and such, it also makes the documentation harder to keep up to date in case of people changing jobs etc.

### Code

Code and code blocks are added to a document using three backticks at the beginning and end. The syntax for Code blocks looks like this:

    ```
    this::yourCodeExample{
        return(true);
    }
    ```

Whereas the syntax for ```inline code``` looks like this:

```
Whereas the syntax for ```inline code``` looks like this:
```

### Admonitions

Admonitions are boxes used for notes, warnings or advice. They come in a variety of styles, but the mainly used ones are Note, Info, Tip, Warning and Important.

The MarkDown code for Admonitions is:

```
!!! info
    All text in the Admonition box must be tabbed one step in to the right.

    The Admonition ends when text starts flowing from the original tab position.
```

!!! info
    All text in the Admonition box must be tabbed one step in to the right.

    The Admonition ends when text starts flowing from the original tab position.

Below are the different styles that are normally used:

=== "Important"

    !!! important
        This Admonition is started with ```!!! important```.

=== "Info"

    !!! info
        This Admonition is started with ```!!! info```.

=== "Note"

    !!! note
        This Admonition is started with ```!!! note```.

=== "Tip"

    !!! tip
        This Admonition is started with ```!!! tip```.

=== "Warning"

    !!! warning
        This Admonition is started with ```!!! warning```.


### Footer

The footer code is as follows (including the three hyphens that make a line between the page and the footer):
```
---

**Author:**

Your Name

**Updated:**

2022-10-10 (substitute your date)
```

## Language

### Style

Keep the language as simple yet precise as possible.

* Check the [Commonly used phrases and terms](/../manual/manual_common_phrases/) page for important words and concepts that you are using. Do not hesitate to add to the list if you are writing about new concepts that are missing.

### English

British or american spelling (e.g. colour - color)?

## Final notes

---

**Author:**

Peter Bergdahl

**Updated:**

2022-10-10