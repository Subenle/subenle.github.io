Command buffers 是用来记录命令的对象，其随后被提交到队列里执行。分为基础命令缓冲和二级命令缓冲，其中基础命令缓冲可执行二级命令缓冲，也可以提交到队列。而二级命令缓冲不能直接提交到队列。

记录的命令包含了如下主要命令：
- 绑定pipeline和描述集(descriptor sets)
- 修改动态状态（dynamic state）
- 绘制命令
- 分发命令（计算）
- 执行二级命令缓冲
- 拷贝缓冲或图像

每个命令缓冲都独立的管理自己的状态。

除非额外声明，在无显式同步的情况下，通过命令缓冲提交的命令可能以随机的顺序执行，或者说是并行执行。在无显式内存依赖的情况下，这些命令对内存的影响也可能不会直接对其他命令可见。对于同一个命令缓冲里的命令或者跨多个提交到队列的命令缓冲都适用。对于隐式和显式的同步，需查看「同步」章节的内容。

## 命令缓冲的生命周期
![VkCommandBufferLifecycle](/assets/vulkan/VkCommandBufferLifecycle.png)
- Initial：命令缓冲创建后即在此状态。之后也可从Recording、Executable、Invalid状态reset到此状态
- Recording：vkBeginCommandBuffer将状态转为Recording，此状态下vkCmd*命令可用来记录命令。
- Executable：vkEndCommandBuffer将状态转为Executable。此状态下可提交、重置或记录进另一个命令缓冲。
- Pending：vkSubmit将状态转为Pending，此状态下应用一定不要再修改命令缓冲。执行完毕后状态会转为Executable或Invalid，这看创建时是否设置了VK_COMMAND_BUFFER_USAGE_ONE_TIME_SUBMIT_BIT。
- Invalid：修改或删除命令使用的资源会导致命令缓冲转为此状态。此状态下的命令缓冲只能reset或释放。

## 命令池 CommandPools
命令池是分配命令缓冲区内存的不透明对象，并允许实现将资源创建的成本分摊到多个命令缓冲区上。  
命令池是外部同步的，这意味着一个命令池不能在多个线程中同时使用。这包括通过记录从该池分配的命令缓冲区中的命令，以及分配、释放和重置命令缓冲区或命令池本身的操作。比如命令缓冲A和B是同一个命令池创建的，那么不能在两个线程上同时分别调用A和B的命令。

相关命令
- vkCreateCommandPool ：创建命令池
```cpp
// Provided by VK_VERSION_1_0 
VkResult vkCreateCommandPool(
    VkDevice device,
    const VkCommandPoolCreateInfo* pCreateInfo,
    const VkAllocationCallbacks* pAllocator,
    VkCommandPool* pCommandPool);
```
- vkTrimCommandPool：修剪命令池，以回收未使用的内存，返回给系统。此操作可能是一个比较重的操作，不要频繁调用。
- vkResetCommandPool：回收所有从此命令池中创建的命令缓冲，所有的命令缓冲重置为Initial状态。
- vkDestroyCommandPool：销毁命令池
## 命令缓冲分配和管理
- vkAllocateCommandBuffers：分配命令缓冲
- vkResetCommandBuffer：重置命令缓冲
- vkFreeCommandBuffers：释放命令缓冲
## 记录命令缓冲
- vkBeginCommandBuffer：开始记录命令缓
- VkCommandBufferUsageFlagBits：声明了命令缓冲的行为
  - VK_COMMAND_BUFFER_USAGE_ONE_TIME_SUBMIT_BIT：命令缓冲只提交一次，每次提交前都需要reset并重新record。
- vkEndCommandBuffer：结束命令缓冲的记录
## 提交命令缓冲
提交操作可能会有较高的开销，因此应用程序应尝试将工作批量处理在尽可能少的 vkQueueSubmit 或 vkQueueSubmit2 调用中。所提交的命令开始执行的顺序和其在pSubmits中出现的顺序一样，但是运行结束的顺序是乱序的。
- vkQueueSubmit、vkQueueSubmit2：提交命令缓冲
  - vkQueueSubmit2可提交受保护的命令缓冲
## 二级命令缓冲的执行
二级命令缓冲不能直接提交到队列。要记录一个二级命令来执行，将其记录进一个基本命令。
- vkCmdExecuteCommands

参考资料：
1. [Command Buffers](https://registry.khronos.org/vulkan/specs/1.3-extensions/html/chap6.html)
