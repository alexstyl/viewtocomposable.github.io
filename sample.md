# From TextView to Text

`Text` is the direct replacement of `TextView`. It includes all standard `TextView` customization and many more. Customizations can be passed directly as parameters to the composable. You can extract all customization into a `TextStyle` that you can use through your app. You can provide a `TextStyle` directly into a `Text` as a parameter or via theming.

Example of usage:

```kotlin
Text("hello from Compose")
```

## How to customize your text (text color, size, max lines, etc)

The `Text` composable supports functionality you would expect such as changing its `textSize`, setting its `maxLines` or its `color`.

In addition to standard `TextView` functionality, you get the option to modify the text's `letterSpacing`, `lineHeight` and more.

## How to reuse your text styles

One of the most important parameters of `Text` composable is the `style` parameter. It allows you to pass a `TextStyle`. This works in a very similar way to `TextView`'s `TextAppearance`.

A `TextStyle` contains all customizations you can apply as parameters to a `Text` composable. The benefit is that you can reuse the created style in multiple Text composables in your app. This makes styling consistent across the app and makes your composables easier to read and maintain.

A typical `TextStyle` looks like this:

```kotlin
val h1 = TextStyle(
    fontFamily = FontFamily.Default,
    fontWeight = FontWeight.SemiBold,
    fontSize = 42.sp
)
```

which you can use in your `Text` like this:
```kotlin
Text("My big header", style = h1)
```

Alternatively, you can define the style in your application's `MaterialTheme` like this:

```kotlin
val Typography = Typography(
    h1 = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.SemiBold,
        fontSize = 42.sp
    )
)

@Composable
fun MyTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        typography = Typography,
        content = content
    )
}
```

and use it in your composable:

```kotlin
MyTheme {
    Text("My big header", style = MaterialTheme.Typography.h1)
}
```

## How to use custom Fonts

One of the parameters of `Text` is `fontFamily`. Compose provides some built-in fonts you can use that are device dependant, using the `FontFamily` object. 

If the passed font family is not available on the device, a default font will be used instead.

Example:
```kotlin
Text("This text is written in cursive", fontFamily = FontFamily.Cursive)
```

The built in fonts are `SansSerif`, `Serif`, `Monospace`, `Cursive`. You can also define your own custom fonts in order to personalize your app further.

## How to create custom Fonts

First, you need to have your fonts stored into your project under your `res/font` folder.

> 💡 You can find a plethora of free to use fonts at [Google Fonts](https://fonts.google.com/).

Each font family might come with multiple files. Each file represents a different weight of the font, such as bold, semi-bold or thin. You will have to copy all those files in your resource folder.

Then, create a new `FontFamily` and link each file to a respective styling:

```kotlin
val Urbanist = FontFamily(
    Font(R.font.urbanist_thin, weight = FontWeight.Thin),
    Font(R.font.urbanist_light, weight = FontWeight.Light),
    Font(R.font.urbanist_regular, weight = FontWeight.Normal),
    Font(R.font.urbanist_medium, weight = FontWeight.Medium),
    Font(R.font.urbanist_bold, weight = FontWeight.Bold),
    Font(R.font.urbanist_italic, style = FontStyle.Italic),
)
```

The font family can be used directly as a parameter to your `Text` composable or via a `TextStyle`. The `TextStyle` can be used in your application's theme.

There is a big chance that you will need to change the font of the entire app. For this purpose, you can use the `defaultFontFamily` parameter of the `Typography` class, which is part of the `MaterialTheme`.

## How to use strings from xml resources?

You can continue using all your translated XML string resources in your composables. Instead of having to rely to a `Resource` or `Context` object, you can use the `stringResource()` function:

```kotlin
Text(stringResource(R.string.my_translated_string))

// stringResource accepts args too
Text(stringResource(R.string.hello_to), "Alex")
```

## How to use string plurals from xml resources?

There is currently no built-in way to use plurals. You would have to create your own function for the job. 

You can use a mechanism called _Composition Locals_ to get the current `Context` from a current composable function. This is what `stringResource()` and other similar resource functions use internally to get a hold of `Context` when needed.

A function for string plurals might look like this:
```kotlin
@Composable
fun quantityStringResource(
    @PluralsRes pluralResId: Int,
    quantity: Int,
    vararg formatArgs: Any? = emptyArray()
): String {
    return LocalContext.current.resources
        .getQuantityString(pluralResId, quantity, *formatArgs)
}
```

which you can use in your app like this:
```kotlin
// "Contact" or "Contacts" depending on the numberOfContacts
Text(quantityStringResource(R.plurals.contact), numberOfContacts)

// "No contact", "1 contact", or "$number contacts"
Text(quantityStringResource(R.plurals.x_number_of_contacts), numberOfContacts, numberOfContacts)
```