# springboot @Schedule定时任务你会用吗

springboot @Schedule定时任务
文章目录
springboot @Schedule定时任务
任务间不允许并发且不允许同任务交叠
任务间允许并发且允许同任务交叠
任务间允许并发且允许同任务交叠和不交迭同时存在
任务间允许并发但不允许同任务交叠
spring cloud多实例任务不并发
总结
任务间不允许并发且不允许同任务交叠
使用springboot 定时任务很简单，只需在启动类或者配置类上添加@EnableScheduling，然后在需要定时执行的类上添加@Conponent注解，最后在需要定时执行的方法上添加@Schedule注解并指定执行方式即可：

启动类：

```java
@SpringBootApplication
@EnableScheduling
public class ScheduleApplication {
    public static void main(String[] args) {
        SpringApplication.run(ScheduleApplication.class, args);
    }
}
```

任务类：

```java
@Component
public class ScheduleTask {
	/**
	 * cron任务
	 */
	@Scheduled(cron = "0/10 * * * * ?")
	public void task1() {
		System.out.println("task1");
	}
    /**
     * fixed rate任务
     */
    @Scheduled(fixedRate = 20000)
    public void task2() {
        System.out.println("task2");
    }
}
```


​		这样可以实现简单的定时任务执行，特别是整个项目只有一个定时任务的时候非常适用，因为不存在任务间的并发，不用考虑线程池。

默认情况下，spring scheduler是单线程的，即任务间默认不并发；并且前后时间点的任务不交迭(overlap)，即同一个定时任务，本次任务执行时间到了，上一次任务还未执行完成，那么本次任务直接放弃，熟悉quartz的应该知道quartz可以在job类上使用@DisallowConcurrentExecution实现类似的功能。

也就是说，默认情况下，springboot scheduler禁止任务间和相同任务的并发。
一下两个例子可以证明：



    @Component
    @Slf4j
    public class ScheduleTask {
    	@Scheduled(cron = "0/10 * * * * ?")
        public void task1() throws InterruptedException {
            log.info("task1");
            Thread.sleep(5000L);
        }
    
        @Scheduled(cron = "0/10 * * * * ?")
        public void task2() throws InterruptedException {
            log.info("task2");
            Thread.sleep(5000L);
        }
    }
    
        /**
         * 2020-03-24 15:42:00.005  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
         * 2020-03-24 15:42:05.008  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
         * 2020-03-24 15:42:10.012  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
         * 2020-03-24 15:42:20.002  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
         * 2020-03-24 15:42:25.005  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
         * 2020-03-24 15:42:30.009  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
         * 2020-03-24 15:42:40.004  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
         * 2020-03-24 15:42:45.010  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
         * 2020-03-24 15:42:50.017  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
         * 2020-03-24 15:43:00.008  INFO 3611 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
         */
    根据控制台输出判断：
两个任务的执行频率是一样的，cron表达式都是一样的，但是在同一个时间点并没有两个任务同时执行的日志；
其中任务依然是在线程池中执行的，但线程池中最多只有一个活跃线程[pool-1-thread-1]，也就相当于是单线程。
因此可以得到默认情况下任务间是无法并发的结论，可以参考源码得到印证。



    @Component
    @Slf4j
    public class ScheduleTask {
        @Scheduled(cron = "0/10 * * * * ?")
        public void task1() throws InterruptedException {
            log.info("task1");
            Thread.sleep(11000L);
        }
    }
    /**
     * 2020-03-24 15:52:50.002  INFO 3659 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 15:53:10.001  INFO 3659 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 15:53:30.003  INFO 3659 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task1
     */
    对于单个任务，如果任务之间的间隔小于任务执行时间，那么当任务准备执行时发现前一次任务还在执行就会主动放弃本次任务。这一点可以从上面的例子输出可以判断出来：10s执行一次的任务，因为执行时间时11s，硬生生变成了20s执行一次。

任务间允许并发且允许同任务交叠
前面“任务间不并发且同任务执行不交叠”是解决并发最保守的方案，那这个“任务间允许并发且允许同任务交叠”就是最开放的解决方案：
首先在启动类或者配置类添加@EnableAsync注解

    @SpringBootApplication
    @EnableScheduling
    @EnableAsync
    public class ScheduleApplication {
    	public static void main(String[] args) {
       		SpringApplication.run(ScheduleApplication.class, args);
    	}
    }
    然后在需要并发的方法或类上添加@Async注解



    @Component
    @Slf4j
    @Async
    public class ScheduleTask {
        @Scheduled(cron = "0/10 * * * * ?")
        public void task1() throws InterruptedException {
            log.info("task1");
            Thread.sleep(11000L);
        }
    
        @Scheduled(cron = "0/10 * * * * ?")
        public void task2() throws InterruptedException {
            log.info("task2");
            Thread.sleep(11000L);
        }
    }
    /**
     * 2020-03-24 16:01:40.015  INFO 3786 --- [cTaskExecutor-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:01:40.015  INFO 3786 --- [cTaskExecutor-2] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:01:50.003  INFO 3786 --- [cTaskExecutor-3] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:01:50.004  INFO 3786 --- [cTaskExecutor-4] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:02:00.004  INFO 3786 --- [cTaskExecutor-5] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:02:00.004  INFO 3786 --- [cTaskExecutor-6] g.c.spring.boot.schedule.ScheduleTask    : task1
     */
    可以看到两个任务同时开始，并且上一个任务未执行完成，后一个任务依然准时开始执行。
任务间允许并发且允许同任务交叠和不交迭同时存在
基于上面的例子，如果只在某一个任务添加@Async注解：



    @Component
    @Slf4j
    public class ScheduleTask {
        @Scheduled(cron = "0/10 * * * * ?")
        @Async
        public void task1() throws InterruptedException {
            log.info("task1");
            Thread.sleep(11000L);
        }
    
        @Scheduled(cron = "0/10 * * * * ?")
        public void task2() throws InterruptedException {
            log.info("task2");
            Thread.sleep(15000L);
        }
    }
    /**
     * 2020-03-24 16:12:40.009  INFO 3851 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:12:55.024  INFO 3851 --- [cTaskExecutor-1] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:13:00.005  INFO 3851 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:13:00.005  INFO 3851 --- [cTaskExecutor-2] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:13:15.011  INFO 3851 --- [cTaskExecutor-3] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:13:20.002  INFO 3851 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:13:35.006  INFO 3851 --- [cTaskExecutor-4] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:13:40.004  INFO 3851 --- [pool-1-thread-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:13:40.004  INFO 3851 --- [cTaskExecutor-5] g.c.spring.boot.schedule.ScheduleTask    : task1
     */
    可以看到

对于同一个任务，只有添加了@Async注解的任务才会交叠执行，而未添加注解的任务依然不会交叠；
对于不同任务之间，虽然整体上是并发的，但是没有添加@Async注解的任务会影响任务间的并发：同一个触发时间点，只有当未添加@Async注解的任务执行完成后，才会将其他Async任务添加到线程池中。
发现控制台打印的线程名后缀是一个无限增长的数字([cTaskExecutor-%d])，推断应该默认创建了一个CachedThreadPool，这个线程池其实可以自己创建和配置，避免可能的OOM，控制台打印的信息也印证了这一点：
2020-03-24 16:12:55.021  INFO 3851 --- [pool-1-thread-1] .s.a.AnnotationAsyncExecutionInterceptor : No task executor bean found for async processing: no bean of type TaskExecutor and no bean named 'taskExecutor' either
1
添加如下配置类：



    @Configuration
    public class TaskExecutorConfigure implements SchedulingConfigurer {
        @Override
        public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
            scheduledTaskRegistrar.setTaskScheduler(taskScheduler());
        }
    
        @Bean(destroyMethod = "shutdown", name = "taskScheduler")
        public ThreadPoolTaskScheduler taskScheduler() {
            ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
            scheduler.setPoolSize(10);
            scheduler.setThreadNamePrefix("task-pool-");
            scheduler.setAwaitTerminationSeconds(600);
            scheduler.setWaitForTasksToCompleteOnShutdown(false);
            return scheduler;
        }
    }

控制台输出如下：

2020-03-24 16:33:00.013  INFO 4044 --- [    task-pool-1] g.c.spring.boot.schedule.ScheduleTask    : task2
2020-03-24 16:33:00.013  INFO 4044 --- [    task-pool-3] g.c.spring.boot.schedule.ScheduleTask    : task1
2020-03-24 16:33:10.002  INFO 4044 --- [    task-pool-4] g.c.spring.boot.schedule.ScheduleTask    : task1
2020-03-24 16:33:20.001  INFO 4044 --- [    task-pool-2] g.c.spring.boot.schedule.ScheduleTask    : task2
2020-03-24 16:33:20.001  INFO 4044 --- [    task-pool-6] g.c.spring.boot.schedule.ScheduleTask    : task1
2020-03-24 16:33:30.001  INFO 4044 --- [    task-pool-1] g.c.spring.boot.schedule.ScheduleTask    : task1
2020-03-24 16:33:40.004  INFO 4044 --- [    task-pool-5] g.c.spring.boot.schedule.ScheduleTask    : task1
可以看到任务间已经开始真正的并发，而允许交叠和不允许交叠的任务依然各自遵守自己的规则。

任务间允许并发但不允许同任务交叠
其实这里跟最保守的方式只有一个区别：使用一个允许更多活跃线程的线程池，而非默认的但一线程池。这里不能使用@Async注解，否则无法保证同任务不交迭。

配置类：

@Configuration
public class TaskExecutorConfigure implements SchedulingConfigurer {

    @Override
    public void configureTasks(ScheduledTaskRegistrar scheduledTaskRegistrar) {
        scheduledTaskRegistrar.setTaskScheduler(taskScheduler());
    }
    
    @Bean(destroyMethod = "shutdown", name = "taskScheduler")
    public ThreadPoolTaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        scheduler.setPoolSize(10);
        scheduler.setThreadNamePrefix("task-pool-");
        scheduler.setAwaitTerminationSeconds(600);
        scheduler.setWaitForTasksToCompleteOnShutdown(false);
        return scheduler;
    }
    任务类
@Component
@Slf4j
public class ScheduleTask {

    @Scheduled(cron = "0/10 * * * * ?")
    public void task1() throws InterruptedException {
        log.info("task1");
        Thread.sleep(1000L);
    }
    
    @Scheduled(cron = "0/10 * * * * ?")
    public void task2() throws InterruptedException {
        log.info("task2");
        Thread.sleep(15000L);
    }
    
    /**
     * 2020-03-24 16:39:30.002  INFO 4106 --- [    task-pool-2] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:39:30.002  INFO 4106 --- [    task-pool-1] g.c.spring.boot.schedule.ScheduleTask    : task2
     * 2020-03-24 16:39:40.002  INFO 4106 --- [    task-pool-2] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:39:50.002  INFO 4106 --- [    task-pool-2] g.c.spring.boot.schedule.ScheduleTask    : task1
     * 2020-03-24 16:39:50.002  INFO 4106 --- [    task-pool-3] g.c.spring.boot.schedule.ScheduleTask    : task2
     */
    可以看到任务间并发无误，同任务不允许交叠。因为任务间不允许交叠，所以同一时间，系统中存在的任务数就是所有定时任务书，因此只需要设置线程池的容量刚好是定时任务数即可，这也是大部分定时任务的真实需求，也能保证不OOM。
spring cloud多实例任务不并发
如果使用了spring cloud或者其他高可用分布式部署方案，一般来说定时任务最好只在某一个实例上执行即可，这时候，sprint boot如何保证定时任务不在多个实例上执行呢？解决方案思路有：

分布式锁：如果有分布式锁，比如用Redis实现的分布式锁，使用实例抢占的方式，谁抢到了就谁执行，没有抢到的主动放弃，还可以实现负载均衡；
使用数据库锁：熟悉quartz集群的应该知道，quartz集群保证任务不在多个实例执行的方式是借助MySQL的行锁(select for update)，这种方式实现简单，也能实现负载均衡，和分布式锁有异曲同工之妙；
借助spring cloud实例列表：每个节点从eureka获取所有注册实例，然后各个节点根据一致性算法计算出应该执行该任务的节点(保证每个节点计算的结果一致)，比如选取最小的一个节点或最大的一个节点，在判断自己是不是该节点，如果是就执行，否则就放弃。这种方法虽然实现简单，但是无法做到负载均衡，除非给任务添加标识(特定任务有现在特定实例执行)，或者用分时间段的方式(特定时间段的任务在特定实例上执行)等来实现负载。
基于第三种方式，有一个简单的实现：

@Slf4j
public abstract class AbstractSchedulingTask {
    @Autowired
    private DiscoveryClient discoveryClient;

    @Autowired
    private Environment environment;
    
    protected boolean canExecute() {
        List<ServiceInstance> instances = discoveryClient.getInstances(environment.getProperty("spring.application.name"));
        log.info("实例列表：{}", JSON.toJSONString(instances));
    
        // 如果没有任何一个实例组册到Eureka，不执行任务
        if(instances == null || instances.isEmpty()) {
            return false;
        }
    
        String currentInstanceId = environment.getProperty("eureka.instance.instance-id");
        log.info("当前实例：{}", currentInstanceId);
    
        // 所有注册到Eureka中的实例按照ID升序排列
        instances = instances.stream().sorted(Comparator.comparing(ServiceInstance::getInstanceId)).collect(Collectors.toList());
    
        return instances.get(0).getInstanceId().equalsIgnoreCase(currentInstanceId);
    }

总结
任务间不允许并发且不允许同任务交叠：单个任务或任务执行时间很短
任务间允许并发且允许同任务交叠：多个任务且要求每个时间点都必须执行，但要注意控制线程池，否则容易OOM
任务间允许并发且允许同任务交叠和不交迭同时存在：满足复杂场景
任务间允许并发但不允许同任务交叠：满足大部分场景
spring cloud多实例任务不并发：多种实现方式