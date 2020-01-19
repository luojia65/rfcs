- Feature Name: `author-attached-crates-io-names`
- Start Date: 2020-01-19
- RFC PR: [rust-lang/rfcs#0000](https://github.com/rust-lang/rfcs/pull/0000)
- Rust Issue: [rust-lang/rust#0000](https://github.com/rust-lang/rust/issues/0000)

# Summary
[summary]: #summary

This RFC document adds a new way to identify one certain crates-io crate by attaching its author along with
the crate name. The document also declares how the crates should be declared in `Cargo.toml` as a dependency.

# Motivation
[motivation]: #motivation

Existing crates-io names declares unique identifier for one given crate. But there are side effects for this method.
Malicious users may take names of popular projects to seize benefits or threat its maintainers. Also, there could be
projects that their maintainers stop to develop which may also cause issues.

We used to regard the crate name itself as an identifier of crate function as well as its author, but recent
experience and ideas suggests it should be separated from the information of the author as there exist side-effects.

The crate name itself only could be inducing or misleading. Some author may take algorithm names, research ideas or
short English words directly to its projects name but didn't finish the project or cannot satisfy most users; when
users need one crate for this algorithm or research idea, they could be led to those unfinished projects. Existing
projects like `flate2` avoids these short names to not be misleading; this could be one of the unwritten rules but
it cannot be assure that all project owners would follow them.

Short crate names could be taken by malicious users to cause damage. Popular project names may be rush-registered
to seize benefits or threat the maintainers. Rust learners could write senstive security code anywhere in order to
study, but attackers would intentionally register popular names and put virus into it. There could be methods to
protect names, but trademark or patent protection may only apply to project author names, but not project names. This
could be especially important for secure transport protocols and cryptography libraries.

Crate quality are not reflected by now in crate names. Authors with good code style and unsafe handling knowledge
cannot title its projects for good quality. For example if there were a crate named `xls-format` then this crate
could be written by anyone with any quality, but if it turned to `the-rust-team/xls-format` and `luojia65/xls-format`
users may prefer the former one directly for good support and quality for the trustive brand `the-rust-team` (for example),
and this brand is registered as a trademark so others cannot take it. By distinguishing good from not so good the
Rust community may raise better projects or even new author brands.

So in order to make the name straight and safe, we introduce author attached crates-io names. We attach author's
name before project name to describe that this author is responsible for this project. We expect this method
to solve the three issues above for misleading, damage and quality.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Here we refer:

- project name: the name of project itself. `awesome-library`
- brand author: the identifier prior to the project name. `luojia65`
- crate name: name of brand author and project name combined. `luojia65/awesome-library`

## `brand-author` cargo-toml package field

Now that we have author attached crates-io names and you have a crate to publish. For every crate you should declare one
name as `brand-author`. You may write `Cargo.toml` file like:

```toml
[package]
name = "awesome-library"
version = "0.1.0"
authors = ["luojia65 <me@luojia.cc>", "another_author <some@example.com>"]
edition = "2018"
brand-author = "luojia65"  # <-- New field
```

Here brand author means this project is maintained under this unique brand. This brand is taken with you crates-io
user name, and can be protected by trademarks or patents. You may put your company, develop team, organization if you
are permitted. If you are permitted to use this brand, your `cargo publish` could success if there are no other problems;
or if you are not, you cannot publish your project under this brand. The brand author can be different from field `authors`.
You may feel free to add all maintainers to `authors` but only fill one leader or your team as a group into brand author
as there should be only one brand author name.

After published your crate are shown in `crates.io` as `luojia65/awesome-library`. There could exist many projects named
`awesome-library` but under different brand author. Then it's time for other users to depend on your crate.

## Use another crate as dependency

Now that your project has attracted one user, who wants to use your library as a dependency. But as explained above, there
could be many crates with equal project name. So the user should write like:

```toml
[dependencies]
"luojia65/awesome-library" = "0.1"
```

Additional quotes are put around the name to follow the toml grammar (bare `/` is not allowed).

When the user use the library in code, it writes:

```rust
use awesome_library;
```

instead of putting the full name in. Or in edition 2015 users may write like `extern crate awesome_library;`.
Now your user feels free to write code with your awesome library.

## Get access to a brand

Time goes on and you are now an active library developer in Rust and often publish libraries. You could be working for a group
or work on your own thus often switch between brands. By the time you successfully registered an account for `crates.io`, you own
one personal brand with your account name. Your group, company or school may register their own accounts to acquire their brands.
If you need to use your group's brand, the account of the group may grant you access for a time period in `crates.io` web settings.
Note that access period could not be permanent and could be revoked. Now with your personal account token logged in you are now
allowed to use your group brand.

If you maintain a group you may open `crates.io`. In the 'account settings' page there is one new feature 'brand access'. Here
you may grant others to use your account name as a brand or you may revoke your former permissions.

```text
crates.io  [ Click or type 'S' to search ] Browse | Docs | [A] Alice

Brand Access ................................ [ Grant new user ]
[ bob ] .... Created 1 year ago. Expires in 4 months. [ Revoke ]
[ cindy ] ... Created 1 month ago. Expires in 7 days. [ Revoke ]
```

## Crate alias

You could be so excellent that your project attracts maintainers to create a goup. Or you may transfer your project to another group.
If older projects still depend on `luojia65/awesome-library` and you want to update project under this name as well as new name, you
may set this as a crate alias. After transfer you may set the old name an alias for `your-group/awesome-library`. Now older projects
referring to old name are still updated. There could be another circumstance that you need to create alias for older version of this
project e.g. `your-project` is an alias for older (but stable) version of `your-project-preview`.

Open any crate at `crates.io` and there could be a new button 'Alias' for 'create alias' in all crates.

```text
[ awesome-library ] 0.1.0 ...... [ Follow ] [ Alias ]
```

Click the 'Alias' button and follow instructions, an alias could be created under your chosen brand.

## If your account is rush-registered

You are taking your account with patent or trademark, but this account was registered by other people. Don't worry. Go `crates.io`
and click new item 'Report' after dashboard and account settings. Submit your trademark or patent certificate to the Rust team and they
would help you to take the account name. If you submit the report, an automatic message is given to that account describing what had
happened, if that account does not react for some time period (e.g. 6 months) and is not marked 'protected', you now own this account.
The team would monitor all the process to prevent rush-register issues.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

## Enhance `Cargo.toml` dependency chooser

Right now we add simply one new line to `[dependencies]` section to declare a dependency. This is written like:

```toml
# type 1
[dependencies]
crate-name = "version"
# type 2
[dependencies.crate-name]
version = "version"
```

Here dependent crates are named without backslash character `/` guaranteed as it's illegal in TOML grammar.
TOML also support map key to be quoted strings like `"crate-name"` other than nake strings. So to allow names with
one backslash, we need to change `cargo` logic to allow string with one backslash to be a dependency name:

```toml
# type 3
[dependencies]
"author-id/crate-name" = "version"
# type 4
[dependencies."author-id/crate-name"]
version = "version"
```

Now cargo should search for crate `author-id/crate-name`. In Rust code the root module of this crate is still `crate_name`.

## Transition from old names to new names

New backslash names could coexist with old naked names in the first period. Eventually the naked names could be
deprecated or finally removed. If developers still need to depend on older projects with naked names only, they
should add its `crates.io` user name prior to the project name itself.

## Modifications on `crates.io`

There are two parts we need to add: Brand access and Report. And there is one thing we should modify is the crate usage guide.

Brand access is shown in page 'Account Settings' in every manu bar addressed `https://crates.io/me`. We add new section
`Brand access` to support using this account as brand author for other accounts. This section contains a button 'Grant new user'
and a list of granted accesses available by now. The 'Grant new user' button leads to a new menu where the user fill in another
user's name, revoke date and provide a confirm button; if confirmed `crates.io` stores target user, time now, time revoked into
database to declare that a new granted user list item is added. The list shows all granted user list items, the time this grant
was performed and will be revoked; for every node user name and a 'Revoke' button is shown and after 'Revoke' button is manually
clicked this list item is deleted in database. This section may show in web page like:

```text
Brand Access ................................ [ Grant new user ]
[ bob ] .... Created 1 year ago. Expires in 4 months. [ Revoke ]
[ cindy ] ... Created 1 month ago. Expires in 7 days. [ Revoke ]
```

Report page is used for patent or trademark owners to retrieve its account. This page could be added into the drop-down manu of
users logged in. This page should contain textboxes to type in E-mail address, retrieve material uploader, additional information
and one submit button. After submit the all filled messages should be deliveered to the site maintaining team. All input boxes should
install with anti-spam algorithms or apply other methods to avoid advertisments or malicious users.

Crate usage guide by now exists for every crate page. For example there are:

```text
[ Logo ] fake-avx512 0.1.0 ................................... [ Follow ]
| Homepage | Documentation | Repository | Dependent crates |
[ Cargo.toml [ fake-avx512 = "0.1.0" ] .......... [ Copy to clipboard ] ]
```

We may replace the instruction for crate name with author identifiers. For example:

```text
[ Cargo.toml [ "luojia65/fake-avx512" = "0.1.0" ] [ Copy to clipboard ] ]
```

## Cargo publish

When cargo is preparing to publish crates, it should check if `brand-author` key exists for `package` section of the configuration
of this crate. This key allows default value to be the author's account name judged by the hash it logged in. If this key exists, cargo
is to publish using the replaced name. Then cargo should check if this account have access to the brand author name provided. If
the API replied okay for access then continue, or reject publishing for illegal access and raise error for it. After access checking
cargo could procceed other steps and finish the publish process.

# Drawbacks
[drawbacks]: #drawbacks

Adding author identifiers could be expensive and adds to the complexity of Rust ecosystem. New users have to learn complex author/name
crate naming system and old users need time to adapt to new naming system.

If we add author prior to project name, it may not be easy for users to remember the full crate name when using.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

One possible design is to declare a prefix only allowing certain accounts to use. Like `tokio-` or `futures-`, this way is already
adapted by the community to describe meta-project and sub-projects, or projects related to it. But now as we allow other accounts to take
arbitrary crate names with some prefix, this possible design forbids it and third-party developers have to think out of other ways to
name its project which could be difficult to judge. And comparing this method with author-attached way this method acts somehow same
but developers should write very long crate name in Rust code or even use complex `use ... as ...` to make names short. Also, this way
do not assure quality of project and short names without prefixes could still be hi-jacked by attackers so the core problem is not
solved perfectly.

Another possible design we could allow multiple projects with the same name exists in `crates.io` and choose which is needed in config
file. However this adds to complexity and could be more misleading for new users. It could also be hard to distinguish between projects
where users may download wrong project which maybe insecure or with not so good quality.

Possible design may put author in other fields other than the name. It may be suggested the spliting character could be `.` other than `/`
so we could use `[dependencies.luojia65.project-name]`. However as TOML only allow quoted key name for strings with dots like
`"luojia65.project-name"`, users may be confused on when the quotes should be writte. TOML parser are in this way not easy to implement
and maintain. Other delimiters like `::` may also considered not proper under this rule.

It's considerable to maintain what the naming system is now and not to change it. If we do not implement this, short names could be taken
like what domain rush-registers and trademark hooligans had done to cause damages and severe issues. It would not also be great for a
long-term maintained programming technology like Rust to allow users to take one name permanently, because in long term this name may have
different meanings as culture advances, and the project in long term may branch, transferred or removed which should not fit for permanent
names.

# Prior art
[prior-art]: #prior-art

Discuss prior art, both the good and the bad, in relation to this proposal.
A few examples of what this can include are:

- For language, library, cargo, tools, and compiler proposals: Does this feature exist in other programming languages and what experience have their community had?
- For community proposals: Is this done by some other community and what were their experiences with it?
- For other teams: What lessons can we learn from what other communities have done here?
- Papers: Are there any published papers or great posts that discuss this? If you have some relevant papers to refer to, this can serve as a more detailed theoretical background.

This section is intended to encourage you as an author to think about the lessons from other languages, provide readers of your RFC with a fuller picture.
If there is no prior art, that is fine - your ideas are interesting to us whether they are brand new or if it is an adaptation from other languages.

Note that while precedent set by other languages is some motivation, it does not on its own motivate an RFC.
Please also take into consideration that rust sometimes intentionally diverges from common language features.

# Unresolved questions
[unresolved-questions]: #unresolved-questions

- What parts of the design do you expect to resolve through the RFC process before this gets merged?
- What parts of the design do you expect to resolve through the implementation of this feature before stabilization?
- What related issues do you consider out of scope for this RFC that could be addressed in the future independently of the solution that comes out of this RFC?

# Future possibilities
[future-possibilities]: #future-possibilities

Think about what the natural extension and evolution of your proposal would
be and how it would affect the language and project as a whole in a holistic
way. Try to use this section as a tool to more fully consider all possible
interactions with the project and language in your proposal.
Also consider how the this all fits into the roadmap for the project
and of the relevant sub-team.

This is also a good place to "dump ideas", if they are out of scope for the
RFC you are writing but otherwise related.

If you have tried and cannot think of any future possibilities,
you may simply state that you cannot think of anything.

Note that having something written down in the future-possibilities section
is not a reason to accept the current or a future RFC; such notes should be
in the section on motivation or rationale in this or subsequent RFCs.
The section merely provides additional information.
