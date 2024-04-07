## Twitter search

### index server/cache

if we do not have inverted index, then search by key words would go through each tweet and find match posts. This is vvery slow.

A very common way is to use inverted index which is from word to a list of twwet id. 

Justin -> [1, 4, 6, 10]
Bibber -> [1, 3, 6, 10]

When seaech these two, we compare these two lists and reutnr common ids which are [1, 6, 10].

Since there could be very words and tweet id. The index cache could be very large.

We need to shard them

1) Shard by words

Pro: No need to query all servers. As all tweet id list is in the same node for this word.

Con: All words starting with J goes to one node. But this could lead to hot spot on some of the nodes. 

2) Shard by tweet id

Hash value of the tweet id determines which node to store.

Pro: Data is reletively evenly distributed. No node will be heavily queired.

Con: Tweet id containing the same word will be distributed among all nodes as well. So need searching all nodes for a given key word. But it is ok we can do this in parallel. So generally this is the wy to go.

Since we need to saerch all nodes, some noes might be slower, longtail. We could set up some threshold. If at this point a lot of nodes already responded, and only a few was left, we send requet again to them.


## Youtube 

### upload video
After uploading the video, the original video will be stored in some blob like AWS S3. And the video need to be processed/encoded. Because videos uploaded need to be streamed on different platforms which requires differnet format of videos.
And different qualities of videos.

After this is done we could push some videos to CDN, store the metadata to db.
Meta data could include videoId, uploadUserId, title, description, category. This could be used later for searching videos.
 


## Instgram

Write and read servers can be seperated.

## data store sharding.

### By userId

For hot user, the data node will be accessed more frequently. The traffic is not evenly ditributed.

Some users have far more posts than others, so teh storage is not evenly distributed as well.

What is data of a user can not fit in this node. Then this user's data is moved to another node.

If this shard fails, then all the users in this shard will not be available.

### By photoId

store data by hashing photo id and mod number of servers.


## news feed

Fanout is the process of delivering a post to all friends. Two types of fanout models are: fanout on write (also called push model) and fanout on read (also called pull model). Both models have pros and cons. 

### pull
fanout on read. The news feed is generated during read time. This is an on-demand model. Recent posts are pulled when a user loads her home page.

Pros:

For inactive users or those who rarely log in, fanout on read works better because it will not waste computing resources on them.
Data is not pushed to friends so there is no hotkey problem.

Cons:

Fetching the news feed is slow as the news feed is not pre-computed.


### push
fanout on write

Pros:

The news feed is generated in real-time and can be pushed to friends immediately.
Fetching news feed is fast because the news feed is pre-computed during write time.

Cons:

If a user has many friends, fetching the friend list and generating news feeds for all of them are slow and time consuming. It is called hotkey problem.(For some popular star who have M followers.)
For inactive users or those rarely log in, pre-computing news feeds waste computing resources.

### hybrid

Since fetching the news feed fast is crucial, we use a push model for the majority of users. For celebrities or users who have many friends/followers, we let followers pull news content on-demand to avoid system overload.


