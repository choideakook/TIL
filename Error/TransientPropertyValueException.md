# TransientPropertyValueException

Transient / Property / Value / Exception

일시적인 / 속성 / 값 / 예외

직역해 보자면 일시적으로 속성값에 문제가 생겼다는 뜻 같다.

## ✏️ 발단

간단한 SSR 프로젝트를 진행하던중

Entity 간의 연관관계가 하나 더 추가되면 좋을것 같다고 생각해 수정해준뒤,

연관관계 편의 method 를 새로 만들어주었다.

<br>

자연스럽게 생성 method 를 수정해야됬고 test 로직도 새로 만들어진 domain 생성 로직을 추가해 주었다.

```java
@Test
    void save() {
        // 새로 추가된 생성 method
        MainTitle profile = MainTitle.createProfile("1", "1", "1");

        Category category = Category.createCategory(profile, "category");
        Long categoryId = caService.createCategory(category);
        Category findCategory = caService.findOne(categoryId);

        Channel channel = Channel.createChannel(findCategory, "channel");
        Long channelId = chService.createChannel(channel);

        Channel findChannel = chService.findOne(channelId);

        assertThat(findChannel.getName()).isEqualTo(channel.getName());
        assertThat(findChannel.getCategory().getName()).isEqualTo(category.getName());

        List<Channel> channels = findCategory.getChannels();
        assertThat(channels.get(0)).isSameAs(findChannel);
    }
```

이렇게 해놓고 시간이 늦어 test 확인 안하고 다음날 test 를 돌려보니 전부 에러가 발생했다.

- ***에러 내용***

```java
org.springframework.dao.InvalidDataAccessApiUsageException: 
    org.hibernate.TransientPropertyValueException: 
    object references an unsaved transient instance - save the transient instance before flushing : 
    project.blog.domain.Category.mainTitle -> project.blog.domain.MainTitle; nested exception is java.lang.IllegalStateException: 
    rg.hibernate.TransientPropertyValueException: object references an unsaved transient instance - save the transient instance before flushing : 
    project.blog.domain.Category.mainTitle -> project.blog.domain.MainTitle
```

- ***Root Cause***

```java
Caused by: org.hibernate.TransientPropertyValueException: 
    object references an unsaved transient instance - save the transient instance before flushing : 
    project.blog.domain.Category.mainTitle -> project.blog.domain.MainTitle
```

<br>

## ✏️ 문제 원인

Root Cause 를 보면 대략 뭔가가 없어서 안된다고 하는것같다.

google 에 검색해 `영속성때문에 나는 오류다. FK로 쓰는 객체가 아직 저장이 안 되서 오류가 난다고 한다.` 라는 문구를 보자마자 느낌이 왔다.

<br>

새로 추가해준 생성 method 를 db 에 추가해주는 로직이 없었다.

<br>

## ✏️ 문제 해결

생성 method 를 data base 에 저장해주니 문제가 해결되었다.

```java
    @Test
    void save() {
        MainTitle profile = MainTitle.createProfile("1", "1", "1");
        // db 저장 로직 추가
        mtService.createProfile(profile);

        Category category = Category.createCategory(profile, "category");
        Long categoryId = caService.createCategory(category);
        Category findCategory = caService.findOne(categoryId);

        Channel channel = Channel.createChannel(findCategory, "channel");
        Long channelId = chService.createChannel(channel);

        Channel findChannel = chService.findOne(channelId);

        assertThat(findChannel.getName()).isEqualTo(channel.getName());
        assertThat(findChannel.getCategory().getName()).isEqualTo(category.getName());

        List<Channel> channels = findCategory.getChannels();
        assertThat(channels.get(0)).isSameAs(findChannel);
    }
```