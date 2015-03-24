---
title: Simple and flexible permissions
author: maryrosecook
layout: post
permalink: /blog/post/simple-and-flexible-permissions-2
categories:
  - Uncategorized
---
At [my day job][1], I'm working on a prototype of a community site. To support the development of experimental features, I've been building a flexible permissions system which can be summaried thus:

user -has-an-> ability -for-an-> item

So, users have abilities for items. That's it. An item is something that needs to have its permissions controlled, a user is a site user and an ability is something the user can do upon the item. Some examples:

Johnny owns Johnny's Diary  
Jenny is friends with Johnny  
Jenny can edit Johnny's Diary

To determine the permissions for something on the site, three questions can be asked: does this user have any of these abilities for this item? which of these items does this user have any of these abilities for? and which users have any of these abilities for this item? Note the permissiveness of letting any one of a list of abilities allow permission to be granted.

The prototype has been coded in [Ruby on Rails][2]. The table that stores relationships has columns for an id, a user id, an ability and an item id. All very straightforward. However, there is one extra column: item type. Because items of different types are stored in different tables, the item type column is required to identify the name of the class of which the item is an instance. When any of the above questions are asked, the relationships table is interrogated by matching the class of the items involved with the values in the item type column.

What makes the system simple and flexible? First, it only consists of one small database table for holding relationships, four methods and 75 lines of code. Second, the system makes no assumptions about the site - all the semantics of relationships are in the item types and ability names used. Third, the relationships table can be understood by humans. Fourth, though it is not recommended, more complex structures can be modelled: relationships between items<sup>1</sup>, reflexive relationships<sup>2</sup>, neuter relationships<sup>3</sup> and state relationships<sup>4</sup>.

<sup>1</sup> Where an item has an ability for another item.  
<sup>2</sup> Where an item or user can have an ability for itself. For example: ownership.  
<sup>3</sup> Where a user (or item) just has an ability, but no item upon which it can act.  
<sup>4</sup> A special type of neuter relationship which models a state of being for a user.

 [1]: http://www.theothermedia.com
 [2]: http://www.rubyonrails.org