There's an urgent need in the Netrunner community moderation of Jinteki.net. The weak identity guarantees of the current account system, coupled with a lack of moderation tools, means that there is very little that admins and mods can do to address toxic behaviour on the site. The following document outlines the minimum functionality needed for viable moderation on Jinteki.net.

# Requirements
## 1. Email verification

In order to effectively ban toxic users, Jinteki.net needs a stronger identity system. Email verification is widely used as a mechanism to limit the number of additional accounts a malicious user can make. It's possible to circumvent bans by creating new email addresses, but this simple measure effectively raises the bar for malicious users, requiring somewhat onerous email account creation and account validation for sockpuppet accounts.

In order to work, email verification must precede access to chat and game creation for new players. The simplest possible implementation of this doesn't allow the user to log in if their email address is unverified, and instead presents them with a screen requesting that they verify their email address, along with the option to resend the verification email.

## 2. Permabans

Moderators should be given the ability to ban toxic users. This could be as simple as a form screen where a moderator enters a user name to ban. Security is an important consideration here, as permaban functions are inherently sensitive; specifically, we should take care to validate that the ban comes from a legitimate moderator on the backend.

## 3. Appeals

When a banned user attempts to log in, they should be shown a screen notifying them they are banned indefinitely and explaining to them how they can appeal the ban. This is mostly required in case of human error or bugs.

