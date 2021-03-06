---
layout: post
title: Configuration
---

In this post I'll give a walkthrough to a simple Yi configuration.

First, note that Yi has no special purpose configuration language.
Yi provides building blocks, that the user can combine to create their
own editor. This means that the configuration file is a top-level Haskell script
, defining a _main_ function.

{% highlight haskell %}

import Yi
import Yi.Keymap.Emacs as Emacs
import Yi.String (mapLines)

main :: IO ()
main = yi $ defaultConfig {
  defaultKm =
      (meta (char 'r') ?>>! increaseIndent)
     -- and use the default Emacs keymap otherwise
     <|| Emacs.keymap
 }


-- | Increase the indentation of the selection
increaseIndent :: BufferM ()
increaseIndent = do
  r <- getSelectRegionB
  r' <- unitWiseRegion Line r
     -- extend the region to full lines
  modifyRegionB (mapLines (' ':)) r'
     -- prepend each line with a space

{% endhighlight %}


In the above example, the user has defined a new _BufferM_ action,
_increaseIndent_, using the library of functions available in Yi. Then, he
has created a new key-binding for it. Using the disjunction operator (<|>), this
binding has been merged with the emacs emulation keymap.

This is a very simple example, but it shows how powerful the approach is: there
is no limit to what functions the user can create. It is also trivial to use any
Haskell package and make its function available in the editor. This is an
obvious advantage over the traditional approach, where only a special purpose
language is available in the configuration file.

Another advantage to this configuration style is is purely declarative flavour.
The user defines the behaviour of the editor "from the ground up". This can be
contrasted to emacs (and lisp) style, where the configuration is a series of
modifications of the _state_.

Finally, I shall say that this configuration model is not unique to Yi: it has
already been used with success in the XMonad window manager.
