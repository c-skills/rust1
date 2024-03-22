Do not Rust your Government
===========================

Recent discussion about memory safety of programs and finger-pointing towards C-programmers
has gone too wild so I am just taking some little notes.

While one of the inventors of C, [Dennis Ritchie](https://en.wikipedia.org/wiki/Dennis_Ritchie) received numerous
awards and the honor of the very same institution - The POTUS - for his achievements (UNIX and C)
its now all `void`.

I am not neglecting the technical work that is done by [CISA](https://media.defense.gov/2023/Dec/06/2003352724/-1/-1/0/THE-CASE-FOR-MEMORY-SAFE-ROADMAPS-TLP-CLEAR.PDF) that resulted in [the White House entering langwars](https://www.whitehouse.gov/oncd/briefing-room/2024/02/26/press-release-technical-report/).
They do have a point, but as always: it depends!

Did they finger-point [Log4Shell](https://en.wikipedia.org/wiki/Log4Shell) that was a devastating *cyber pathogen* (lol) in a
memory-safe language. I am not mentioning all the pwnage that happened due to PHP or how lot of the issues addressed in
[rustsec](https://rustsec.org/advisories/) are actually memory-issues ...

I am stopping here and are not entering the full discussion about memory-safety, GC, performance, confinements,
or full-chain pwnage of the past 50y.

Just giving you a single thing to consider:

Trading Build-Safety for Memory-Safety
--------------------------------------

You want 0day RCE's in memory-safe languages? Here is one, client side:

*Note that the executions come not from the just checked out repo*
<p align="center">
<img src="https://github.com/c-skills/rust1/blob/master/screenshot.jpg"/>
</a>
</p>

As a professional programmer you know the difference between the build machine and the machine(s) stuff is deployed
on. In professional context they are not the same 99% of the time.
You *can* have evil code in the repo that you checked out and after the build it would infect your deployment servers.
Thats bad and can happen to any language. You can also have Trajan code within the `Makefile` that would affect your
build hosts. Thats why you skim through it after checkout for sanity. `Buildroot` for instance is comparing sha256
hashes for all the stuff it downloads during the build.

Pwning the build system is pwning the deployment and likely pwning the developer machines too.

Under no circumstances a `make` should fetch and build recursively *and execute anything that it fetches from the untrusted recursion chain.*
*DevOps* is a thing. You checked that the `Cargo.toml` file is clean after checkout? Great! But did you check the `build.rs` script too?
Did you check the 100 dependencies one by one? Did you check there are no DSO's in any of the repos that `rustc` will load and execute
during build ...

I haven't digged into open issues of `cargo` or `rustc` or whether this topic has already been discussed.
The version I used was `cargo 1.77.0 (3fe68eabf 2024-02-29)` which is the latest I got.

The root-CA case
----------------

It does not even require an evil open-source project to pwn you. It is an open secret that
[root-CAs are hacked](https://en.wikipedia.org/wiki/DigiNotar) and such certificates are traded on the black market.
Sometimes things can [even be easier.](https://blog.mozilla.org/netpolicy/2021/11/04/mozilla-publishes-position-paper-on-the-eu-digital-identity-framework) by making laws or issuing warrants that force providers or distributors to include evil CA's.

Either way, this allows for MiM-ing the recursive fetch&build chain at the very end or anywhere in between without even forensicans
noticing since the modified checkout would only be delivered to certain selected victims, not the broad masses. Good luck
determining what has pwned you 8y ago ...

So the next time someone approaches you about *memory safety* being the cause of destruction of the world,
you can pay back with a balanced view. Not neglecting the validity of some of their arguments.


Offline Compilations ranting
----------------------------

One personal thing that I can't get over when *just compiling* rust programs is that it notoriously contacts/downloads
rustup servers and downloads stuff without my consent. Can you please change that? You wouldn't believe but there is
folks which works offline and just wants to `compile this`, without being forced to go online and to dl 500MB of binaries
before a single line of code is translated.

