---
layout: post
title: Formatted number entry
date: 2016-08-01 08:00:00
author: Antonio Feregrino
summary: I faced the necessity of having a &quot;formatted textbox&quot; where the user could enter a given integer number and have it formatted instantly to a comma separated number.
featured_image: featured.png
images_folder: /xamarin-forms/formattednumberentry/
github: https://github.com/ThatCSharpGuy/FormattedNumberEntry
lang: en
tags: XamarinForms, Xamarin
featured_tag: XamarinForms
---

While developing an app using Xamarin.Forms I faced the necessity of having a "formatted textbox" where the user could enter a given integer number and have it formatted instantly to a comma separated number, for example: `1234567` → `1,234,567`, because our users often found themselves counting the number of zeros they had entered into the small textbox.  

This doesn't sound too complicated (and it isn't). Using an `Entry` we can easily hook up to the `TextChanged` event and perform the following tasks: 

 0. Stop listening for changes on our `Entry` `Text` property 
 1. Take the `Entry` text, lets name it `oldText`
 2. Parse `oldText` into a number, lets name it `number`
 3. Format `number`, and place the formatted text in `newText`
 4. Set the `Text` property of our entry to `newText`
 5. Start listening for changes on our `Entry` `Text` property


 I don't want to wire/unwire the event handler every time I use it, so let's create a control that inherits from `Entry` and override the `OnPropertyChanged` method:

```csharp  
public class FormattedNumberEntry : Entry
{
    protected override void OnPropertyChanged(string propertyName = null)
    {
        if (nameof(this.Text).Equals(propertyName))
        {
            if (!_shouldReactToTextChange) return;

            _shouldReactToTextChange = false;

            var oldText = this.Text;
            var number = DumbParse(oldText);
            var newText = $"{number:#,###}";

            this.Text = newText;

            _shouldReactToTextChange = true;
        }
        base.OnPropertyChanged(propertyName);
    }
}
```  

By the way, you see that `DumbParse` method there? it is just that, a dumb parsing method that ignores non-digit chars:

```csharp  
public static int DumbParse(string input)
{
    var number = 0;
    int multiply = 1;
    for (int i = input.Length - 1; i >= 0; i--)
    {
        if (Char.IsDigit(input[i]))
        {
            number += (input[i] - '0') * (multiply);
            multiply *= 10;
        }
    }
    return number;
}
```  
  
This is the final result:

<img src="http://i.giphy.com/1zNrHjJ4dawbm.gif" />

Thhat's it, we're done.


## Noooooo, wait.

Even though we may think that our mission was accomplished, our brand new control lacks of a good user experience. For example, see what happens when the user tries to erase a number located in the middle:

<img src="/images/xamarin-forms__formattednumberentry__careterror.png" title="Error" />

See how the cursor jumps to the end (or start, depends on the platform)? the same happens after writing a number. Let's fix that.

## Problem  

`Entry` doesn't have a `CursorPosition` property so we need to create a simple <a href="https://developer.xamarin.com/guides/xamarin-forms/custom-renderer/" target="_blank" rel="nofollow">custom renderer</a> since only at native level the underlying controls expose such information. In this case, we will attach an event handler to each platform specific *TextChanged* event and inside such handler we need to:  

 0. Stop listening for changes on our control's `Text` property 
 1. Get the current cursor position
 2. Take the control's text, lets name it `oldText`
 3. Parse `oldText` into a number, lets name it `number`
 4. Format `number`, and place the formatted text in `newText`
 5. Set the `Text` property of our control to `newText`
 6. Calculate the new cursor position
 7. Set the new cursor position
 8. Start listening for changes on our control's `Text` property

### iOS 
For iOS we will subscribe to the `EditingChanged` event and work all our magic there:

```csharp  
public class FormattedNumberEntryRenderer : EntryRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            Control.EditingChanged -= Control_EditingChanged;
        }
        if (e.NewElement != null)
        {
            Control.EditingChanged += Control_EditingChanged;
        }
    }
```  

Now, in the `Control_EditingChanged`: 

```csharp  
var element = ((FormattedNumberEntry)Element);
// Oh boy, thank you internet: http://stackoverflow.com/a/34922332

// 1. Stop listening for changes on our control Text property
if (!element.ShouldReactToTextChanges) return;
element.ShouldReactToTextChanges = false;

// 2. Get the current cursor position
var selectedRange = Control.SelectedTextRange;

// 3. Take the control’s text, lets name it oldText
var oldText = Control.Text;

// 4. Parse oldText into a number, lets name it number
var number = FormattedNumberEntry.DumbParse(oldText);

// 5. Format number, and place the formatted text in newText
var newText = $"{number:#,##0}";

// 6. Set the Text property of our control to newText
Control.Text = newText;

// 7. Calculate the new cursor position
var change = -1 * (oldText.Length - newText.Length);
var newPosition = Control.GetPosition(selectedRange.Start, (nint)change);

// 8. Set the new cursor position
if (newPosition != null) // before we fail miserably
{
    Control.SelectedTextRange = Control.GetTextRange(newPosition, newPosition);
}

// 9. Start listening for changes on our control’s Text property
element.ShouldReactToTextChanges = true;
```  

Now works great: 

<img src="/images/xamarin-forms__formattednumberentry__iosgood.gif" title="iOS working goooooood" />

### Android
For Android we will subscribe to the `AfterTextChanged` event and create all the formatting there:

```csharp  
public class FormattedNumberEntryRenderer : EntryRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            Control.AfterTextChanged -= Control_AfterTextChanged;
        }
        if (e.NewElement != null)
        {
            Control.AfterTextChanged += Control_AfterTextChanged;
        }
    }
```  

And then in the `Control_AfterTextChanged` implementation:

```csharp  
var element = ((FormattedNumberEntry)Element);

// 1. Stop listening for changes on our control Text property
if (!element.ShouldReactToTextChanges) return;
element.ShouldReactToTextChanges = false;

// 2. Get the current cursor position
var cursorPosition = Control.SelectionStart;

// 3. Take the control’s text, lets name it oldText
var oldText = Control.Text;

// 4. Parse oldText into a number, lets name it number
var number = FormattedNumberEntry.DumbParse(oldText);

// 5. Format number, and place the formatted text in newText
var newText = $"{number:#,##0}";

// 6. Set the Text property of our control to newText
Control.Text = newText;

// 7. Calculate the new cursor position
var change = oldText.Length - newText.Length;

// 8. Set the new cursor position
Control.SetSelection(cursorPosition - change);

// 9. Start listening for changes on our control’s Text property
element.ShouldReactToTextChanges = true;
```  

Here is the final result

<img src="/images/xamarin-forms__formattednumberentry__androidgood.gif" title="Android working goooooood" />

### Universal Windows Platform  

For the Windows platforms we need to handle the `TextChanged` event:

```csharp  
public class FormattedNumberEntryRenderer : EntryRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            Control.TextChanged -= Control_TextChanged;
        }
        if (e.NewElement != null)
        {
            Control.TextChanged += Control_TextChanged;
        }
    }
```  

Then in `Control_Text` changed:  

```csharp  
var element = ((FormattedNumberEntry)Element);

// 1. Stop listening for changes on our control Text property
if (!element.ShouldReactToTextChanges) return;
element.ShouldReactToTextChanges = false;

// 2. Get the current cursor position
var cursorPosition = Control.SelectionStart;

// 3. Take the control’s text, lets name it oldText
var oldText = Control.Text;

// 4. Parse oldText into a number, lets name it number
var number = FormattedNumberEntry.DumbParse(oldText);

// 5. Format number, and place the formatted text in newText
var newText = $"{number:#,##0}";

// 6. Set the Text property of our control to newText
Control.Text = newText;

// 7. Calculate the new cursor position
var change = -1 * (oldText.Length - newText.Length);
if (cursorPosition + change < 0)
    change = 0;

// 8. Set the new cursor position
Control.SelectionStart = cursorPosition + change;

// 9. Start listening for changes on our control’s Text property
element.ShouldReactToTextChanges = true;
```  

And *voilà*:

<img src="/images/xamarin-forms__formattednumberentry__uwpgood.gif" title="Android working goooooood" />

## Wrapping up  
I know, I know, it might seem like a lot of code for such a *simple* task, yet, I couldn't find how to do it "natively" let alone using Forms. This kind of small details improve the user experience of our apps and aren't too complicated to implement. As always, feel free to browse the code (<a href="https://github.com/ThatCSharpGuy/FormattedNumberEntry" target="_blank">available on GitHub</a>) or *tweemail* me if in doubt.  

## Future improvements
This control isn't perfect, it has a lot of room for improvements:

 - Modify the parsing algorithm to allow bigger numbers to be input
 - Allow decimal numbers
 - Save at control level the parsed number. Currently anyone interested in getting the integer value from the control will have to parse the text.
 - The *convert-to-string* method may have issues with globalization 

So go ahead and have cross-platform fun.