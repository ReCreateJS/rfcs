- Start Date: 2020-03-12
- RFC PR: https://github.com/ReCreateJS/rfcs/pull/3
- Relevant Project: All CreateJS

# Redeploy Website

## Summary

Move the createjs.com home page so that it is hosted on Github. 

## Motivation

The createjs homepage is an essential part of the project.  It has information for users, tutorials, demonstrations and a link to the current build of each module and the combined build.  Notably, the current and combined builds have not been updated to reflect updates to the source files.  The home page needs to be maintained and updated.

## Detailed design

1. Create a new repository in the CreateJS space called HomePage.  

2. Copy all the website files to that repo.

3. On Github settings, add createjs.com as a custom domain that points to that repo.

4. Update the DNS for createjs.com so that it is served by Github.

## Drawbacks

It's hard to know what GrantSkinner will think about this and  whether they'd be okay with giving up the keys to the homepage.  Maybe it has some IP value.

## Justification

The advantage of hosting it on Github is that changes can be made by consensus and in a transparent way.  The homepage would be maintained in the same way that the source files.  This would allow people to view and comment on any changes.

## Alternatives

An alternative would be for someone who works at GrantSkinner to make changes as requested.

Another option would be to make a new homepage for createjs with a new domain name.  But even if that is done, it would still be a good idea to host it on Github.

## Unresolved questions

I'm assuming that the homepage is a static site that just includes HTML, javascript, css and images.

It would be good to check how the site was initially built.  Looks like Bootstrap was used.  I don't know if any other generating software was used to build the html files or whether each page was manually created.  Looking at the number of pages and simplicity, I think it could be feasible to just edit it in place rather than rebuilding from any source.
