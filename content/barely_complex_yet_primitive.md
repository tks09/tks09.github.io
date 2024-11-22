---
title: "Barely complex enough, yet as primitive as possible"
date: 2023-08-22T23:14:53+02:00
taxonomies:
  tags: ["gudesoftware", "dev"]
---

This will be the first post for an ongoing series I want to write on what makes a software well designed or not. Of course, I'm clear that this is probably one of 1203901831 articles of this kind in the internet. But as everything in this blog, this is also just about writing things down which helps me personally to find my own opinion to those things.

As mentioned in the [about me](/about) page, "gude" is a typical colloquial language to say "hi" in german. Gude is rooted from "gut", which is good in english.

There's a whole philosophy on good and you can probably discuss this in a whole semester of philosophy courses and still discuss if we talking on good as in good food, or good as in public good? Let's start with todays statement:

# Barely complex enough, yet as primitive as possible

This is somehow the obvious "KISS" statement. Don't try to overcomplicate things. Also, one of my learnings is to not abstract too more than you need to. If you want to solve a specific business purpose, then solve it. Don't think too much about what else could be solved if you would just make the whole code more abstract and generic, if you don't have a specific usecase for it.

Example:
My task is to write a little app which is able to store books into a database and has a web frontend for this. Nothing else needed since it's just running on a single users local machine at the library next door.

**Best design: I find an existing solution for it, which is well designed.**

*Good design*: I chose my favourite tool and wrote a GUI which is able to give a simple local book database management to the librarian. Stored in a file, backed up on a NAS or Dropbox or whatever.

*Bad design*: I'm the best programmer in the world. So I added usermangement and additionally to that I made the storing of objects in the database generic, so you can store any object you like. This brings no obvious feature for the user, but we can later extend it easily. Data is stored in some SQL database on some cloud hoster so it's accessible everywhere. It just took me 2 weeks.

What I solved with the bad design solution is a whole different task, which is: write an application with usermanagement which is able to store any kind of objects into a SQL database table, which is hosted in a cloud environment.

This is not just bad because I took more time for the implementation than necessary, but also because all those little features I added because I love my tools and I want to show what I'm capable of. One rule of thumb I learned is that features always lead to more features. And feature is complexity. So complexity leads to complexity. And as [as we learned from grugbrain](https://grugbrain.dev/#grug-on-complexity): complexity very, very bad. 

**By the way talking about features leading to features. I introduced usermanagement, right? What about permissions now? Do we need roles?** You see, features lead to features. And features are complexity.

