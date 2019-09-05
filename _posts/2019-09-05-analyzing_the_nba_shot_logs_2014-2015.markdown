---
layout: post
title:      "Analyzing The NBA Shot Logs 2014-2015"
date:       2019-09-05 16:54:48 -0400
permalink:  analyzing_the_nba_shot_logs_2014-2015
---

<img src=https://www.itsnicethat.com/system/files/072017/59636abf7fa44cb082006e80/images_slice_large/OCD_Commercial_NBA_Its_Nice_That_list.jpg?1499687638>

# Introduction
With massive amounts of readily available data, professional sports teams, like most businesses, rely heavily on data analytics to guide their evolution.  The NBA is no different.  The game looks very different than it did 30, 20 or even 5 years ago.  In the past the game was dominated by “Big Men” who played close to the basket.  The offense was centered around getting the ball first to the center near the basket.  The center would either take the shot or pass to an open man if double teamed.  The 3-point shot was considered a risky low percentage shot. Rather than a first option, it was considered a second or third option.Analytics has changed the mindset and offensive strategy of many, if not all teams.  Teams began to realize the risk was worth the reward.  The idea that 3 is always greater than 2 began to take hold.  Shooting 10 3-point shots at 40% is still better than shooting 10 2-point shots at 50%.  Now teams like the Houston Rockets base their offense on a two-shot outcome.  Shoot a 3-pointer or take the highest percentage shot within 5 feet from the basket.  Teams like the Rockets have reversed the older paradigm.  They spread the floor with four shooters beyond the 3-point arc and have one “big man” under the basket.  They look to get the ball to an open 3-point shooter first to take the shot.  If the defender under the basket comes out to help defend the 3-point shooters the ball is then passed to the one person under the basket for a dunk or a lay-up.  The mid-range shot is not completely dead.  There are still players like Chris Paul and Dirk Nowitzki who are good enough shooters form anywhere on the court that they always have the green light when they are open.  Here we will look at some of the best close-range, mid-range and long-range shooters. As well as defenders.  Good defense never goes out of style.  We will also use machine learning to classify or “predict” shots made or missed and who won the game.  Rather than recap this entire project I will pass along some tips I found along the way for producing interactive visuals using Plotly offline and Tableau Public. As well as some of the downfalls of using the free non-membership versions of these products.

# Interactive Plotly Visuals Using Plotly Offline
If you are like me. I had seen Plotly visualizations before.  Loved their interactivity, and you know, just how cool they look.  But was lost on how to produce them in my own notebooks.  Plotly offers a free no membership version where you can produce fully interactive plots within your Jupyter notebooks.  There is also a membership version that I will leave to you to investigate.  It offers a way to host your visualizations online.  The downfall of the offline version is that your visualizations will be fully interactive in your notebook but can only be saved for export as static .png files.  Thus, the plots I display here will not be interactive, as they are in my notebook.  That’s Ok.  I am more interested in passing along some of the code structure that I uncovered for producing these plots.  I will also include the necessary imports to get started.

# Plotly Imports
To get started I used the following imports
```
from plotly import tools
import plotly.offline as py
import plotly.graph_objs as go
py.init_notebook_mode(connected=True)
```
 
 # Creating a Stacked Bar Chart
Of course, your data will be different.  This will give you the idea of the syntax for how Plotly visualizations are structured.  Plotly uses different traces to plot different data on the same canvas. In this case each trace represents the plots to be stacked in the bar chart.

```
trace1 = go.Bar(
    x=df1[two_point_shots]['FGM'].value_counts().index.values,
    y=df1[two_point_shots]['FGM'].value_counts(),
    name='2-Point Field Goals'
)
trace2 = go.Bar(
    x=df1[three_point_shots]['FGM'].value_counts().index.values,
    y=df1[three_point_shots]['FGM'].value_counts(),
    name='3-Point Field Goals'
)

data = [trace1, trace2]
layout = go.Layout(
    barmode='stack',
    title='Count of Made and Missed 2 and 3 point Field Goals',
    xaxis = dict(
        tickmode = 'array',
        tickvals = [0, 1],
        ticktext = ['Missed', 'Made']
    ),
    yaxis=dict(
        title='Number of Shots'
    )
)

fig = go.Figure(data=data, layout=layout)
py.iplot(fig)
```

# Plotly Graph Made and Missed 2 and 3-pointers
<img src=https://lh3.googleusercontent.com/SN1oCLqx0KOl6ZM9gSmWbBCj1lrKw_eUoaukoMI3iUi54C9HjUybpz7377cgncZjsTSUn-rx97z9UV3qQxfbP09YncOGGaiJUdu8GOiAjVTHq29PCmOJcYuvAB4s6Ql1UFiE-k7b-Ybtbva7bVnOEvxW_NppXzpkjvfJbxjbg4b4wZAfLJoh66ClCiiulQZTzD8CBr3O_p9qlOT-2eBGa1lTXf4e_dmi79ultFhjnPUKqkUehANT0_vwscIrGnHA9XdRfU348NOQpbDy-ivG1bwIjEqRercBt22P6LOgLEX-pnX2b1naII-Cpj5r4CWI8PPkdqrrs_RHH7kaJ33UPNvy_SenKfNIIykdIcwjEnuYwyiNwJ4RLZExGKb7t2BgKRTcE9r7YzjTZIV8YKSjg60CKAi1UctRc78w1p43oRiJBpqbfm6RwLlCqeFBA8CSLoHFI7uX8X46MQDegm9HqxpZUMpkn5ocpmqBPcreOvHa1QJtwx6mwieJkJd-d4IOJhdL4nZOql4_laqxSuCM8aYh0hP_cm0vmOJ5rgJwf_SPlR1aKYyD0IWJYze3Gt9_WqDYiLOi7MoS3HzteEL3Zjj_K-GlbM1LVpcZHTFgmsHBADYdbAowF96wj0s4OHlQR-ulIPASy9mcSdjR_uuKSXsbBsqkivcqqTDD7PREvzrhfFdxduKlpD0=w700-h450-no width="850">

As noted, I had to export the plot as a png file for display here.  However, you get the idea of how the plot is produced.  The plot is fully interactive when produced in your notebook.  It gives the totals for each shot type, made and missed, when you hover over the plot with your cursor.

# Plotly Multiple Line Charts on Same Canvas
Code template used for creating multiple line charts on the same canvas

```
trace1 = go.Scatter(
    x=two_point_shots_by_period.index,
    y=two_point_shots_by_period['percentage'].values,
    name='2-Point Field Goal'
)
trace2 = go.Scatter(
    x=three_point_shots_by_period.index,
    y=three_point_shots_by_period['percentage'].values,
    name='3-Point Field Goal'
)

data = [trace1, trace2]
layout = go.Layout(
    barmode='group',
    title='Field Goal Percentage by Quarter',
    xaxis = dict(
        tickmode = 'array',
        tickvals = [1, 2, 3, 4, 5, 6, 7],
        ticktext = ['Q1', 'Q2', 'Q3', 'Q4', 'OT1', 'OT2', 'OT3']
    )
)

fig = go.Figure(data=data, layout=layout)
py.iplot(fig)
```

# 2 and 3 Point Shooting Percentage by Quarter and Overtime Periods
<img src=https://lh3.googleusercontent.com/n526XJhgeU-90mR_mcynru3wvprA2d4UjsJvJ82gfXkquZ8voKuBL4Ak3JFZJQCAX2Kh3-LzIAv5GOryAVZk7AtdEgDzeRyDSoNZjGTDqXtDsq63JKr72I78jWDyduhbosVAuVCmh4Ww7NhmhHek4Cq51EhyGxesKcDn4sRs3vCsQDErkL4_kB9_Z6OKJK6uYFQw3hU8Xqex9DL4J9xy2-4zlyGepdL5In_IIdM9Vrakthh0N_pg3IpD633WC093tFCZc7P99pP0rwRl7rqv6jakt089iJuAEyei9k-rQ7ADSgyosjSa8-a5cvPBQ33kL264eqWsw_2bNmfnwbxP1VtqEQ7G5pGG8klkCFgEZZtu3Od_CW4OVDCGPnqdFk_zkg6UPNBorHMrh5dAH3n8hiP5NnuFZGAbfTvgJt1dvtdDifTjQ-gfDkbzJSmaYWrwx30Cb79VlubQAXg5UwuA9tRob-iJvdQktrr91wUQLUUKCSVUg58svmjcehJkbbOalRe-2TIS-TNYGsk5dgKK1LavuVi9Pjy2eB8yXkxqKa6xsuxjxaywF0jU4hEhe4fZXIGTVDb2qEA4ytfYs-76fi_82epN1Ldw5_0q2vaMfsMGl2GqCKUvVzAe_I7bvNn_BgkKK6cwHgAcH94nTyxVQadnG1HIzg5anGMMKJ1OMneDVdwR7XWRDTU=w700-h450-no width="850">

# Plotly Scatter Plot
Here I will include the code I used to produce a scatter plot looking at how the closest defender affects shot distance in the NBA.

```
trace1 = go.Scattergl(
    x=df1['SHOT_DIST'],
    y=df1['CLOSE_DEF_DIST'],
    mode='markers',
    marker=dict(
        opacity=0.5
    )
)

data=[trace1]
layout = go.Layout(
    title='How Shot Distance is Affected by Closest Defender')
    
figure = go.Figure(data=data, layout=layout)

py.iplot(figure)
```


# How Shot Distance is Affected by Closest Defender
<img src=https://lh3.googleusercontent.com/_1LX_5PDiHFoVRzstZXZfhMmVvkFdIUFPXDPjPDxkZFVBYHQLeoca8bWjrzCqhR16ZRXlZT_qPuKDNhuhbXthwTkM48GRPMbo9DzuozdioB_q2Q-lxIhTaiLnpgyjmx2UAB0oYTb7Sgc5kbtQSxDIS4bGiEt9EHOHbOcHJaMiNZLHyvLtrCa-RywwHCopksxDnuoiTNnXipt-AZGibp8qqnGqtjCt3GL8itdAdfl0HcItqFVOJFYvXybcyb70p_hm6D_RG76DPzNyhtKPessX9LlhCMh2PBd8UdfNXG6NaAjSVZM-vBZ0I3LEvn0ncnjDVner8gvUz1ZdIOcZOdma4Jytnv3le2t8oEr2a0ZiiysqglLdRb1M8x4sKiNXMQN7y-m8lC567SmVzcat58EwcqLCxMlFipPfaO968oyvMc3gjFNY8Yk94FemcBIxX2VPHoz4bx0nKN3JAguZNZHaBq_mdISV9W5hIlXIWWRpD22vOO1x1r3blYb8h0vSCC7OdjPPx_fsZ5WM3bs_xiJMoGORO9dVUm6nmx8GJVxRiUj0QieLhbCrfxDqKYcGDvU5OattXYzl_t5wwE1cnr1L7H5PI7hGCFP-gH6-AGVBTAErMihoz9U4nJWuGutF5akGaYgWrhjwftSXhmLP_XcxmMkibm9Uo0h_v6Bas2WCF63MKlkfoTjAYE=w700-h450-no width="850">

This plot did give some insight on how the closest defender affects shot distance.  However, more interesting to me was to see the distribution of shot distances.  The three-point line is at 23.75 feet in the NBA.  You see the largest aggregation of shots close to the basket and near the three-point line.  This lends credence to my theory that these two shots ranges have become the most prevalent in the NBA.

# Brief Introduction to Tableau Public
I had no experience with Tableau prior to this project.  It is a powerful tool for visualization.  I wanted to incorporate some Tableau visuals into my final project.  Tableau is a paid software product but does have a public free “lite” version.  Of course, with a few caveats and limitations.  You can download the Tableau public desktop app to work with and create your visualizations locally.  However, you must create a free account and save all your visualizations on the publicly hosted site.  You will not be able to save the visualizations locally.  Once saved on the public site you can download them back to your local machine for changes and editing.  The visualization created in Tableau Public can be displayed in your Jupyter notebook using the code listed below.  Once saved on Tableau Public click the share button.  You will have two options.  A link that will display the visualization as a web page from your profile and the HTML source code.  Paste the HTML code into your notebook with the code below for a fully interactive visualization in Jupyter.  You can also have your visualization interactive in PowerPoint by creating a new slide and clicking add as a web page using the link provided.  Again, this will not help us here.  The Tableau visualization I will show here has to be added as a static png file so it will not be interactive.  Still a bit more functionality than Plotly offline

# Code to Embed Visualization from Tableau Public
Once your visualization is saved on Tableau Public.  Click the share button and copy the code listed in embed code.  Then paste in your notebook as follows.

```
%%HTML
<paste copied source code for visualization>
```

Run the cell and your visualization will be fully interactive

# Best Mid-Range NBA Shooters Using Tableau Public
The following is an example of a visualization I created using Tableau Public.  It is not interactive here.  The markdown format used here requires a static png image.  However, it is fully interactive in my notebook using the code listed above.  I will also be able to have an interactive view in my slide presentation by creating a new slide and inserting the visualization web page url. 

<img src=https://lh3.googleusercontent.com/vCOX2-rN70LSfbKOG841whw0VQ3htXFQjEP6YXPh2B35Imo5YCVCw1KlQTdkFPzWOYe7pxi3Aq9TSgJuu3yFmlymeZYDqrT0eSzK2CXAMURsHMIQOoewBRdsGlImvsickTWtfz6i43cd2j8ov9iUue24QHUNi3ZJsBItmPU396TwPfhNBbm6p8UNjlRXz894SqPOJMX8KadHrLoJW4CWAyhiSS148WjoI5VkBB8TOkT75Et2Gxot0BtKI4Zk6Mjc6eEaS1zy9FS9TiuzMTwk3nAMLCoin5CwxB79i-q4V9tK_cCoV32ou6BLvdEt61Qt9xspcDw_qVX0mjCsR3kY0ehuyyJbxqnJ-Cqb_bR58mqoSlg5x8PHkJFSNQ80xP7iTtmWyst_6IBHl2ar7pxl5ivMTaKcjU0-TLHfS0ZYFMsDUKTpmH85tG6yufOTzun-6CoyVuNlSSB01fcNW5YZxbLpOzKApL7MmMnaOKi6CgU7DFpRbel1E48EkNgz9CQVuYqZWmUyrA83eG9wylfMNvlHhDBGY2VVfhC1EtvaLlL2Mo91c5n7l845psKuy0ApkPHG81TKj2D8ErfqX3zQa5Eo9CdQ1RY7LGsqXAV0689HyRB_zT0lfCh9iXRHh0FQV50VwUj-9Ic4JddIM3OcCvwQyxzxx2S5UlbhKw1nlpq-LE39JW6yzSg=w1378-h822-no width="850">

As we can see the mid-range shot is not dead! Less prevalent but not dead.  Probably because guys like Chris Paul shows up on the league's best Mid-range and Long-range shooters.  While guys like Dirk Nowitzki show up on the league's best close-range and mid-range shooters.  Some guys have the green light regardless of where they are on the court.

# Conclusions
Until now I have shied away from using powerful tools like Plotly and Tableau.  For some very simple reasons.  I had no experience with them, they seemed a bit intimidating and I was just barely getting the hang of things like Matplolib and Seaborn.  Like many things I have learned in data science and programming I just kept digging until I found examples that gave me the framework to get something working.  Once I had something working, I could then experiment.  I gave some insight into some the cool things I found in my analysis of the NBA shot logs.  However, I wanted to leave you with something useful.  Some working examples of Plotly code that helped me. Having a working framework helped me to understand how Plotly operates.  My best advice for Tableau is just to download it and start working with it.  Watch some of the videos on Tableau Public.  Watch some YouTube videos.  Then just start playing with it.  It is self-explanatory how to import an excel workbook or csv file.  Then just start experimenting.  It is a lot of drag and drop experimentation and Tableau does a lot of the heavy lifting.  I am a Noob to Tableau by any measure, but I was still able to produce some impressive graphics relatively easily.  Hope this helps!






