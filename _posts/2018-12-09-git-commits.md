---
layout: post
title: "Git Commits"
date: 2018-01-11 10:00:00 -0600
categories: jekyll update
---

For the last few weeks, I've been on a pairing tour with various software crafters as part of my apprenticeship. Exposure to new technologies aside, I find that one of the most helpful aspects of the pairing tour is exposure to better programming habits. Git commits in particular caught my attention this week. Seeing a cleaner git history and comparing it to my own, better commits are something I should work to improve.

There is a lot of information out there on better git commits, so nothing here is new. It does help internalize it to document in this fashion though. A typical starting point for exploring this topic seems to be this example provided by Tim Pope in a 2008 blog:

```
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary.  Wrap it to about 72
characters or so.  In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body.  The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase can get confused if you run the
two together.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug."  This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Typically a hyphen or asterisk is used for the bullet, followed by a
  single space, with blank lines in between, but conventions vary here

- Use a hanging indent
```

This paragraph from the [git-commit Manual Page](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html#_discussion) is also frequently mentioned:

```
Though not required, it’s a good idea to begin the commit message with a single short (less than 50 character) line summarizing the change, followed by a blank line and then a more thorough description. The text up to the first blank line in a commit message is treated as the commit title, and that title is used throughout Git.
```

Developers commonly capture the sum of this example in the following seven guidelines:

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain what and why

###1. Separate subject from body with a blank line

In Pope's example, we see this as:

> Capitalized, short (50 chars or less) summary
>
> ...

There is a clear visual distinction between the short subject line and the body of the commit provided by this blank line. How can we achieve this?

`git commit`: This is the most basic way, of course. `git commit` launches a text editor, allowing you to enter whatever text you wish. Simply add your commit message with Pope's example as a guide.

`git commit -v` or `git commit --verbose`: Perhaps you are having trouble writing your commit message because you can't recall everything you've changed. `git commit -v` allows you to enter a mode similar to `git commit`, expect you will also see a `git diff` for easy reference.

Not all commits require a subject and body though. Perhaps you've made some minor changes to the ReadMe of your project and nowhere else. Including a body to your commit might be excessive. In this case you can use `git commit -m` to make your commit just the 50 character subject line:

`$ git commit -m"Fix typos in ReadMe"`

Between this short message and use of a `git diff`, this subject line conveys enough meaning for future programmers.


###2. Limit the subject line to 50 characters

As the git-commit Manual Page says, 50 character subject lines is not a requirement. However, in addition to making variations of git log easier to review, it forces you to really think about the core of your commits. If the subject line is difficult to formulate in 50 characters, it may reveal a commit which is too large.


###3. Capitalize the subject line

This guildline is rather straightforward. It is simply an issue of using

> Fix typos in ReadMe

versus

> fix typos in ReadMe


###4. Do not end the subject line with a period

Again, another straightforward guideline. This one is as simple as using

> Fix typos in ReadMe

versus

> Fix typos in ReadMe.

Aside from grammatically enforcing this as a subject, excluding the period results in less clutter and helps reduce your character count. (I have no clue why, but I've always included the period.)


###5. Use the imperative mood in the subject line

According to wikipedia, "[t]he imperative mood is a grammatical mood that forms a command or request."

Git uses imperative for its built-in messaging: `Merge pull request #1 from user/branch`. By also using imperative mood, we keep our messages consistent with an already existent style.

To write subject lines in the imperative mood, you may find it help to think, "This git commit will..." and fill in the rest.

I personally have a tendency to write commits like, `Fixes typo in ReadMe`. If we use the suggestion above, we see the second subject line below makes more sense:

> *This git commit will fixes typo in ReadMe.*
> *This git commit will fix typo in ReadMe.*


###6. Wrap the body at 72 characters

Git does not handle text wrapping. In order to maintain a clean commit, it is recommended that text be wrapped at 72 characters.

Meeting this guideline is easy enough. If you modify your `.vimrc` to include `filetype indent plugin on` this should use `textwidth=72` for `filetype=gitcommit`.

I have read recommendations for using Pope's `fugitive.vim` for this and other Git functionality in Vim. I have not explored this yet.


###7. Use the body to explain what and why

I've seen a lot of paragraphs on this guideline. For me, it's easy to think of it like this:

> Your commit explains the what and why; your code explains the how.

If you find it difficult to explain the what and why in a short summary, your commit may be too large. You may consider splitting it up.


###Additional Recommendations

In general, programmers seem to think highly of Pope's commits. For further examples of better, cleaner commits, you can look no further than [his GitHub page](https://github.com/tpope).
