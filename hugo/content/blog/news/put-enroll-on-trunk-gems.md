---
title: Put Enroll On Trunk Gems
date: 2023-11-19
linkTitle: Put Enroll On Trunk Gems
author: Trey Evans
---

This is our first attempt to fully upgrade enroll to all 'trunk' IdeaCrew gems - and get them to install simultaneously.

By the end we'll be upgrading several other gems Enroll relies on and discovering some dependencies we didn't know existed - including between gems that **aren't** ours and the Dry gem suite.

### The First Pass

The first thing we did was change all the references in the `Gemfile` to IdeaCrew repository gems to 'trunk', and remove the locked versions and SHAs in the `Gemfile.lock`.  As a result we had to bump a bunch of other gems like Nokogiri.

### Things We Learned

1. `dry-schema` and `dry-configurable` don't play well together when `dry-schema` is `< 1.7.1` and `dry-configurable` is `0.13.0`.  `dry-configurable` doesn't provide some of the methods it needs to work with that version of `dry-schema`.  We couldn't step up to a version of `dry-configurable` which corrects this bug (which would be `0.14.0` or higher), because those require Ruby 2.7.x.
2. `dry-configurable` version `0.13.0` does something nasty:
isn't completely honest about what it means by 'deprecate positional arguments' for default settings - it doesn't continue to support them while warning you - it discards the default - making it nil when you really don't expect it.
3. `devise-jwt` and `warden-auth_jwt` rely on `dry-configurable` - and are impacted by changes/issues with in it - especially the default positional argument change.  Odds are if you change `dry-configurable` you'll need to check if you need to upgrade these as well.
4. `nokogiri`, between versions 1.10.x and 1.13.x, stops removing ERB tags when performing scrubbing.  The main way this matters is that Nokogiri is used under the hood by `ActionView::Base.full_sanitizer`.  We use this in an Enroll checker for missing translations to check for missing translations outside of ERB tags.  Since this translation checker accidentally had a dependency on the ERB tags being stripped to provide correct results, I had to update the translation checker to restore that functionality.