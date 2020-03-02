# CreateJS RFCs

Many changes, including bug fixes and documentation improvements can be implemented and reviewed via the normal GitHub pull request workflow.

Some changes though are "substantial", and we ask that these be put through a bit of a design process and produce a consensus among the CreateJS core team.

The "RFC" (request for comments) process is intended to provide a consistent and controlled path for new features to enter the framework.

## The process

In short, to get a major feature added to CreateJS, one must first get the
RFC merged into the RFC repo as a markdown file. At that point the RFC
is 'active' and may be implemented with the goal of eventual inclusion
into CreateJS.

* Fork the RFC repo http://github.com/recreatejs/rfcs
* Copy the appropriate template. For most RFCs, this is `0000-template.md`
Copy the template file to `text/0000-my-feature.md`, where
'my-feature' is descriptive. Don't assign an RFC number yet.
* Fill in the RFC. Put care into the details: **RFCs that do not
present convincing motivation, demonstrate understanding of the
impact of the design, or are disingenuous about the drawbacks or
alternatives tend to be poorly-received**.
* Fill in the relevant projects.
* Submit a pull request. As a pull request the RFC will receive design
feedback from the larger community, and the author should be prepared
to revise it in response.
* Update the pull request to add the number of the PR to the filename and 
add a link to the PR in the header of the RFC.
* Build consensus and integrate feedback. RFCs that have broad support
are much more likely to make progress than those that don't receive any
comments.
* Eventually, the core eam will decide whether the RFC is a candidate
for inclusion in CreateJS.
* ...

## TODO - define:

* When you need to follow this process
* Finalize process
* Who reviews these
* Checklists

## Inspired by

https://github.com/emberjs/rfcs
