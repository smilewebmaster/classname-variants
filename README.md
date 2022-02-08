# classname-variants

Stitches-like [variant API](https://stitches.dev/docs/variants) for plain class names.

The library is framework agnostic and can be used with any kind of CSS flavor.

It is especially useful though if used with [Tailwind](https://tailwindcss.com/) and React, as it provides some [dedicated helpers](#React) and even allows for a _styled-components_ like API, but with class names instead of styles!

# Basics

Let's aussume we want to build a button component with Tailwind CSS that comes in different sizes and colors.

It consists of some _base classes_ that are always present as well as some optional classes that need to be added depending on the desired _variants_.

```tsx
const button = variants({
  base: "rounded text-white",
  variants: {
    color: {
      brand: "bg-sky-500",
      accent: "bg-teal-500",
    },
    size: {
      small: "px-5 py-3 text-xs",
      large: "px-6 py-4 text-base",
    },
  },
});
```

The result is a function that expects an object which specifies what variants should be selected. When called, it returns a string containing the respective class names:

```ts
document.write(`
  <button class="${button({
    color: "accent",
    size: "large",
  })}">
    Click Me!
  </button>
`);
```

# Advanced Usage

## Boolean variants

Variants can be of type `boolean` by using `"true"` as the key:

```tsx
const button = variants({
  base: "text-white",
  variants: {
    rounded: {
      true: "rounded-full",
    },
  },
});
```

## Compound variants

The `compoundVariants` option can be used to apply class names based on a combination of other variants.

```tsx
const button = variants({
  variants: {
    color: {
      neutral: "bg-gray-200",
      accent: "bg-teal-400",
    },
    outlined: {
      true: "border-2",
    },
  },
  compoundVariants: [
    {
      variants: {
        color: "accent",
        outlined: true,
      },
      className: "border-teal-500",
    },
  ],
});
```

## Default variants

The `defaultVariants` option can be used to select a variant by default:

```ts
const button = variants({
  variants: {
    color: {
      neutral: "bg-gray-200",
      accent: "bg-teal-400",
    },
  },
  defaultVariants: {
    color: "neutral",
  },
});
```

# React

The library contains utility functions that are useful for writing React components.

It works much like `variants()` but instead of a class name string, the resulting function returns an object with props.

```ts
import { variantProps } from "classname-variants/react";

const buttonProps = variantProps({
  base: "rounded-md text-white",
  variants: {
    color: {
      brand: "bg-sky-500",
      accent: "bg-teal-500",
    },
    size: {
      small: "px-5 py-3 text-xs",
      large: "px-6 py-4 text-base",
    },
    rounded: {
      true: "rounded-full",
    },
  },
  defaultVariants: {
    color: "brand",
  },
});
```

This way a compontents' props (or part of them) can be directly spread into the target element. All variant-related props are used to construct the `className` property while all other props are passed through verbatim:

```tsx
type Props = SX.IntrinsicElements["button"] & VariantProps<typeof buttonProps>;

function Button(props: Props) {
  return <button {...buttonProps(props)} />;
}

function App() {
  return (
    <Button size="small" color="accent" onClick={console.log}>
      Click Me!
    </Button>
  );
}
```

# Bonus: styled-components, but with class names 💅

Things can be taken even a step further, resulting in a _styled-components_ like way of defining reusable components:

```tsx
import { styled } from "classname-variants/react";

const Button = styled("button", {
  variants: {
    size: {
      small: "text-xs",
      large: "text-base",
    },
  },
});
```

# License

MIT
