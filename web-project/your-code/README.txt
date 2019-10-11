https://slides.com/jvbw/youtube-scraper

I wanted to scrape a playlist of a documentary series I enjoy, that's on youtube : How It's Made.

Series 29 : https://www.youtube.com/playlist?list=PLQvvxnU2-ItePyupBYGFLlKJ5SZap2y8L

From the original page of Season 29, with all the links, I wanted to scrape every video in the playlist and extract :
    - Title -done
    - Description -done
    - Date it was added -done
    - Length -done
    - Nb of likes and dislikes -done
    - Nb of views -done
    - Top comment (with user name) - for fun mostly -not done



###### Initialization
Getting every URL from the playlist page, pushing headers.



###### Getting the links for all the videos in the playlist, from the playlist page.

Gets the "href" element, and creates a string by adding "youtube.com" at the beginning.
Returns a list containing the urls of every video.
The video thumbnail elements were in a TABLE that wasn't visible in the inspector.
I (with Eldiias's help) had to analyze the HTML soup to locate the table.
This is the first place were I use a word in the playlist TITLE, and were the code is specific to this playlist.

 -------------  getlinks(playlist_soup) 

###### Soup from each URL

Taking an individual link as an input, this function returns the soup from the video page.
 
-------------  videosoup(url)

###### Video Title

This function returns the title of the video, in string type.

-------------  gettingtitle(video_soup)
NB : In this function, I added a "no title available" option in case the title tring is empty.
This occurs if the video is unavailable.
This measure is mainly error handling, and the video doesn't get scraped later on.

###### Nb of views

This function returns the number of views, in integer type. It actually retrieves the text : "(nbofviews) vues". So we strip the "vues".  This is specific to french youtube.

-------------  nbviews(video_soup)

###### Nb of likes and dislikes

Likes and dislikes are both contained in a "button arial-label" block.
They are always positionned in the same blocks and easy to retrieve.
These functions return the likes and dislikes in integer type.

-------------  likes(video_soup)
-------------  dislikes(video_soup)

###### Duration

The length of the video is in the metadata, in seconds.
I'll extract it using regex, then convert it to datetime to make classifying easier in the DataFrame.

-------------  duration(video_soup)

###### Short Description
The short description gives basic info about the video.
In the case of our playlist, it gives the season, episode number, and subject.

This part contains some error handling : some of the videos in the playlist are BLOCKED in France.
When a video is blocked, the short description is empty, and thus the result for the regex search is None. This rises an error in the code and ends the loop when building the dataframe.

To avoid this, a flag variable "p" is added, with a value if the description is empty and a different value when the description exists. The function "shortdesc" returns the description (or "None") and the value of p that will be used later in error handling. 

-------------  shortdesc(video_soup)

###### Publication Date

Again, the publication date is present in the metadata.
I converted it to datetime format for easier classifying in the dataframe.

-------------  pubdate(video_soup)

###### Episode number, suject

From the short description. These functions will be run (or not) according to the value of the control variable p, describe earlier.

-------------  episodenb(descriptiontxt)
-------------  subject(descriptiontxt)

###### Video thumbnails urls

This function runs from the original playlist page and not each individual video page.

-------------  getimglinks(playlist_soup)


###### The loop

Here, the functions loop over every link in the link list.

The info from every function is added to a list, that is added to the data frame at the end of each iteration.

If the video is blocked, the error handling takes over and jumps to the next link with "continue", ending that iteration early.

A counter is used to create the index, updated after adding the row to the dataframe.


############ Visualizing the Dataframe with thumbnails for each video 

First, I reorganize the column names, to get the thumbnail next to the subject.
I'll also create a new dataframe for this part.
Now, in order to use the HTML module and display the images, 
I must reformat the thumbnail urls.
I will use a simple function to add an img src tag and specify a display width.

Then, using the HTML function from the IPython.core.display library: 

pd.set_option('display.max_colwidth', -1)

HTML(yt_viz.to_html(escape=False ,formatters=dict(image=path_to_image_html)))


########### GENERALIZING THE CODE FOR ANY YOUTUBE PLAYLIST

In my second code page, Youtube_Spider, I generalized the code using a spider available thanks to Ironhack, customizing it to fit my code.
This code provides some error handling, and is more interactive.
It asks the user if they want to save the data in CSV.
It only provides the short description and not the "episodenb" and "subject" like the How It's Made code.