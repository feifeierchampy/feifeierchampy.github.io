---
layout: post
title: Java并发编程-Callable、Future用法
category: 技术
tags: Java并发编程
keywords:
description:
---



- 从Java 1.5开始，就提供了`Callable` 和 `Future`，通过它们可以在任务执行完毕之后得到任务执行结果

#### Callable
- `Callable`位于`java.util.concurrent` 包下，它也是一个接口

```
public interface Callable<V> {
    /**
	     * Computes a result, or throws an exception if unable to do so.
		      *
			       * @return computed result
				        * @throws Exception if unable to compute a result
						     */
							     V call() throws Exception;
								 }
								 ```

								 #### Future
								 Future就是对于具体的Callable任务的执行结果进行取消、查询是否完成，获取结果
								 必要时可通过`get()`方法获得执行结果，该方法**会阻塞直到有结果返回**

								 ```
								 package java.util.concurrent;

								 public interface Future<V> {
								     // cancel方法用来取消任务，如果取消成功则返回true，如果取消任务失败则返回false
									     // 参数mayInterruptIfRunning表示是否允许取消正在执行却没有执行完毕的任务，
										     // 如果设置true，则表示可以取消正在执行中的任务
											     boolean cancel(boolean mayInterruptIfRunning);

												     // 表示任务是否被取消成功
													     boolean isCancelled();

														     // 表示任务是否已经完成
															     boolean isDone();

																     // 获取执行结果，这个方法会产生阻塞，会一直等到任务执行完毕才返回
																	     V get() throws InterruptedException, ExecutionException;

																		     // 用来获取执行结果，如果在指定时间内，还没获取到结果，就直接返回null
																			     V get(long timeout, TimeUnit unit) throws InterruptException, ExecutionException, TimeoutException;
																				 }
																				 ```

																				 由此可判断Future可提供三种功能
																				 - 判断任务是否完成
																				 - 能够中断任务
																				 - 能过获取任务执行结果

																				 ### 使用

																				 使用场景：
																				 多次请求网络wiki图片url

																				 ```
																				     private class TaskGetChannelByName implements Callable<ChannelObject> {
																					         private String channelName;
																							         public TaskGetChannelByName(String channelName) {
																									             this.channelName = channelName;
																												         }
																														         @Override
																																         public ChannelObject call() throws Exception {
																																		             // TODO Auto-generated method stub
																																					             return getChannelByName(channelName);
																																								         }
																																										     }  
																																											 ``` 

																																											 ```
																																											               // 任务队列
																																														          List<Future<ChannelObject>> resultList = new ArrayList<Future<ChannelObject>>();

																																																         // 保存返回的结果
																																																		         List<ChannelObject> channelList = new ArrayList<ChannelBean.ChannelObject>();

																																																				         ExecutorService executorService = Executors.newCachedThreadPool ();

																																																						         for (int i = 0; i < channelInfos.size(); i++) {
																																																								             Future<ChannelObject> channelObject = executorService.submit(new TaskGetChannelByName(channelInfos.get(i).name));
																																																											             resultList.add(channelObject);
																																																														         }
																																																																        // 循环获取结果，只有在task isDone时才调用get方法获取结果
																																																																		       // 避免了阻塞在某一个任务中
																																																																			          // 不过此方法还有一个bug就是某个任务一直在执行的话，会一直在循环里
																																																																					         //
																																																																							        // 故需要做超时限制
																																																																									       // 比如在Task方法里
																																																																										          // 
																																																																												         // 正常情况下过程总耗时为所有任务都执行完毕的时间
																																																																														         while (!resultList.isEmpty()) {
																																																																																             for (int i = 0; i < resultList.size(); i++) {
																																																																																			                 if (resultList.get(i).isDone()) {
																																																																																							                     try {
																																																																																												                         channelList.add(resultList.get(i).get());
																																																																																																		                     } catch (InterruptedException e) {
																																																																																																							                         // TODO Auto-generated catch block
																																																																																																													                         e.printStackTrace();
																																																																																																																			                     } catch (ExecutionException e) {
																																																																																																																								                         // TODO Auto-generated catch block
																																																																																																																														                         e.printStackTrace();
																																																																																																																																				                     }
																																																																																																																																									                  // 该任务已经执行完毕，从任务队列里移除
																																																																																																																																													                      resultList.remove(i);
																																																																																																																																																		                  }
																																																																																																																																																						              }
																																																																																																																																																									          }  
																																																																																																																																																											         executorService.shutdown();  
																																																																																																																																																													 ``` 

																																																																																																																																																													 考虑该模型的使用场景：
																																																																																																																																																													 - 需要得到所有并发返回的结果时
																																																																																																																																																													 - 和 生产者-消费者模型的区别？
																																																																																																																																																													 生产者-消费者的适用场景是 生产者，消费者两端的操作都比较耗时的是否，
																																																																																																																																																													 而该`Callable` ， `Future`是只有一方是耗时操作
																																																																																																																																																													 实际上相当于有多个生产者，只有一个消费者

																																																																																																																																																													 ---








