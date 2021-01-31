# java-and-sql-test

## SQL
```SQL
CREATE TABLE `topic` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `post` (
    `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
    `topic_id` int(11) unsigned NOT NULL,
    `title` varchar(16536) NOT NULL,
    `text` varchar(16536) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `topic_id` (`topic_id`),
  CONSTRAINT `post_ibfk_1` FOREIGN KEY (`topic_id`) REFERENCES `topic` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

```
Considering the schema definition above, please write the queries that return:
* A. The first 10 topics in alphabetical order
* B. The name of each topic with 5 or more posts.
* C. All the topics without any post.

## Java
* A. Using the tables from above, write the code of the Java classes to model them, including the JPA annotations needed for persisting them. Getters and setters can be omitted.  

* B. Analyze the following code: 
```Java
public class TopicService {
    
    @PersistanceContext
    private EntityManager entityManager;

    @Transactional
    public void updateTopic(long topicId, TopicDTO updated) {
        Topic topic = entityManager.find(Topic.class, topicId);
        if (topic != null) {
             topic.setName(updated.getName());
        } else {
             throw new EntityNotFoundException(Topic.class, topicId);
        }
    }
}
```
1. Please, enumerate all the queries executed when the updateTopic method is called, including transactional sentences. Point out in which line code of the aforementioned method each query is executed.

2. Imagine that the method updateTopic is called from an update method of a RESTful MVC controller named TopicController. Please, point out the first line of the HTTP request that would be the result of calling such method (specify HTTP method and URL where you would have mapped the controller method).

3. In the event that the result is an EntityNotFoundException being throw, what would be the HTTP status that the TopicController should return to its clients?

* C. Analyze the following code:
```Java
public class PostService {
    
    @PersistenceContext
    private EntityManager entityManager;

    private int pageSize = 25; // In real life would be configurable

    public List<PostDTO> listPostTitlesAndTopics(int pageNumber) {
        List<Post> posts = entityManager
            .createQuery(“SELECT p FROM Post p”)
            .setFirstResult((pageNumber - 1) * pageSize)
            .setMaxResults(pageSize).getResultList();
        List<PostDTO> result = new ArrayList(posts.size());

        for(Post post : posts) {
            PostDTO postDto = new PostDTO();
            postDto.setId(post.getId());
            postDto.setTitle(post.getTitle());
            postDto.setTopicName(post.getTopic().getName());
            result.add(postDto);
        }

        return result;
    }
}

```
Which bottlenecks towards the DB does the listPostTitlesAndTopics method present, and how would you fix them?

* D. During a code review, we found the following code fragment:
```java
@lombok.Data
class DateBucket {
	final Instant from;
	final Instant to;

	public static List<DateBucket> bucketize(ZonedDateTime fromDate, 
			ZonedDateTime toDate, 
			int bucketSize, 
			ChronoUnit bucketSizeUnit) {
	    List<DateBucket> buckets = new ArrayList<>();
	    boolean reachedDate = false;
	    for (int i = 0; !reachedDate; i++) {
	        ZonedDateTime minDate = fromDate.plus(i * bucketSize, bucketSizeUnit);
	        ZonedDateTime maxDate = fromDate.plus((i + 1) * bucketSize, bucketSizeUnit);
	        reachedDate = toDate.isBefore(maxDate);
	        buckets.add(new DateBucket(minDate.toInstant(), maxDate.toInstant());
	    }

	    return buckets;
	}
}
```
1. Explain briefly what is the bucketize method trying to do.
2. Refactor the method to use Java 8 Streams.

* E. Describe a design pattern that you have used before (other than Singleton), the problem you had to resolve, and how did the pattern helped you to fix such problem.

## Modeling
In an already existing system for apartment buildings expenses management, the following classes were found:

* Building
* Apartment
* Owner
* Expense

Which relationships do you consider should be among the classes described above? You may add a diagram.

It was necessary to repair the stair landing zone, with a cost of $10.000. Which class would you assign the responsibility of registering and storing that expense to? It may not be among the 4 initial classes. Write the method signature. 

The end of the month is near, and expenses have to be settled. The settlement process consists of:

* Calculate the total expenditures of the period.
* Divide them among the apartments.
* Issue the billing documents.
* Notify the owners.

Which classes would you assign to be responsible for each step? They may not be among the 4 initial classes, in which case you will have to name and describe briefly the fundamental responsibility of the new classes.

## English

Write a few lines below to describe the biggest challenge you had in your professional life and how you solved it. 
