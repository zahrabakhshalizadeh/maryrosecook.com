---
title: Creating a music recommendation engine
author: maryrosecook
layout: post
permalink: /blog/post/creating-a-music-recommendation-engine-2
categories:
  - Uncategorized
---
I have spent the last six weeks writing a music recommendation engine, theperceptron.com It was fun.

**From the user's perspective:**

  1. Enter a band you like.
  2. Get recommendations for other bands you might also like.
  3. Test out the artists recommended by visiting their Myspaces and websites, reading their Wikipedia summaries and listening to sample tracks.
  4. Say whether you like or dislike your recommended bands.
  5. Add promising bands to your playlist so you can listen later.
  6. Suggest an artist or two that the site didn't recommended.
  7. Get on with your life.

**From the code's perspective:**

Recommendations are made based on connections between artists. These connections are found in data taken from the internet:

  * Recommendations made by actual humans: tinymixtapes.com and epitonic.com and users of the perceptron.
  * Artist admiration: artists' top friends on Myspace.
  * Artists on the same mixtape: muxtape.com
  * Artists on the same record label: wikipedia.org
  * Artists posted to the same mp3 blog: hypem.com
  * Artists who have played gigs together: myspace.com

Each rating action that a user can perform on a recommended artist - liking or disliking, visiting websites, listening to songs or adding them to the playlist - is associated with a certain number of points. These points are used in two ways. First, each source has a running total of points given to the recommendations made by the source. Second, each artist connection has a running total of the number of points it has accrued.

Recommendations are given a score based upon these point totals. Ignoring the weightings of the source and connection score, a recommendation's score is calculated thus:

*score = (source\_points + connection_points) / num_source_connections*

the perceptron's algorithm is pretty obvious. What makes the site good is the choice of data sources. However, the algorithm does allow experimentation with adding data sources. If I add a bad one, the scores given to its recommendations drop very rapidly. It only took about 200 user rating actions to get the site's data source weights pretty good. Here is the current table (higher numbers are better):

<table class="admin_table">
  <tr>
    <td class="admin_table_td">
      Source
    </td>

    <td class="admin_table_td">
      Score
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Epitonic similar artists
    </td>

    <td class="admin_table_td">
      0.439
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Tiny Mix Tapes similar artists
    </td>

    <td class="admin_table_td">
      0.316
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Myspace top friends
    </td>

    <td class="admin_table_td">
      0.128
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Mixtapes
    </td>

    <td class="admin_table_td">
      0.075
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Record labels
    </td>

    <td class="admin_table_td">
      0.020
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Epitonic other artists
    </td>

    <td class="admin_table_td">
      0.016
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      MP3 blogs
    </td>

    <td class="admin_table_td">
      0.003
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      Gigs
    </td>

    <td class="admin_table_td">
      Score hasn't settled, yet.
    </td>
  </tr>

  <tr>
    <td class="admin_table_td">
      the perceptron user recommendations
    </td>

    <td class="admin_table_td">
      Score hasn't settled, yet.
    </td>
  </tr>
</table>
