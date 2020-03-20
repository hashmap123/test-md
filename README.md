### 头文件

### 代码块
```cpp

typedef struct
{
    void *mqtt;                                    //主节点相关参数指针，gateway_route_init以后进行赋值
    void *Param;                                   //云平台参数结构体
    void *haspmap;                                 //保存节点的hashmap指针
    char node_subtopic[PUBTOPIC_MAX_LENGHT+1];    //节点订阅主体格式（MQTT协议）id/mac/noderv/#
    char pubtopic[PUBTOPIC_MAX_LENGHT+1];         //节点发布主体格式（MQTT协议）id/mac/nodetx/NodeName
    char mac[MAC_MAX_LENGHT+1];                   //当前节点MAC地址，自定义的节点名称就是唯一的虚拟MAC地址
    char id[ID_MAX_LENGHT+1];                     //当前节点的ID,如果是子节点，id为网口的MAC地址
} gateway_route;


typedef struct
{
    char Nodeid[MAC_MAX_LENGHT+1];              //外设子节点ID，UART, 485, IO，以及自定义的
    AW_MSGQ_DECL(msgq_rev, 100, 8);             //节点接收消息队列实体:100条消息，每条4字节,队列中只存数据指针与数据长度
    AW_MSGQ_DECL(msgq_send, 100, 8);            //节点发送消息队列实体:100条消息，每条4字节，队列中只存数据指针与数据长度
	  int  register_falg;                         //是否需要把节点注册到云端,注册以后标记置位1
	  char devicename[MAC_MAX_LENGHT+1];          //节点名及自定义的
} gateway_node_context_t;



typedef void* gate_T;
typedef void* node_T;

typedef int  (*gate_iterate)( void *key, void *data);
typedef void (*gate_mqtt_msgArrived)(void *data,int len,void *topic,int topiclen);


#define GATE_OK            0
#define GATE_FAIL          -1




#define  MQTT_Protocol  "TCP_MQTT"   //实时性强的场合
#define  HTTP_Protocol  "TCP_HTTP"   //仅仅上报，对下发指令时延敏感
#define  HTTPS_Protocol "TCP_HTTPS"   //仅仅上报，对下发指令时延敏感
#define  TCP_Protocol   "TCP"    //透明传输，自定义协议头
#define  UART_Protocol  "UART"   //透明传输，自定义协议头

/**
 * \brief 创建一个网关,用于存放节点与数据实体
 * \attention
 * \param[in]   void
 *
 * \retval  any_t
 */
gate_T gateway_create(void);


/**
 * \brief 销毁一个网关
 * \attention
 * \param[in]   gate_t
 *
 * \retval  INT
 */
int gateway_destroy(gate_T);

/**
 * \brief 创建一个网关节点，一般为一级节点UART
 * \attention
 * \param[in]   node_name,节点名称一定是具有唯一性的路径
 *
 * \retval  网关节点对象
 */

node_T create_node(char *node_id,char * node_name);


/**
 * \brief 把链路中的节点加入到路由表中，此时正是产生了一个结点设备
 * \attention
 * \param[in]   node_t，网关路由表，   节点路由
 *
 * \retval  　<0,失败，=0成功；
 */

int gateway_join_node(gate_T, node_T dev);



/**
 * \brief 删除一个网关节点
 * \attention
 * \param[in]   gate_T，网关路由表，   node_name节点名称
 *
 * \retval  　<0,失败，=0成功；
 */

int gateway_delete_node(gate_T, char *node_name);


/**
 * \brief
 * \attention
 * \param[in]   gate_T，网关路由表，   node_name节点名称,dev查到到的节点信息
 *
 * \retval  　<0,失败，=0成功；
 */

int gateway_get_node(gate_T gt,char *node_name,node_T *dev);


/**
 * \brief 网关中加入的所有节点遍历
 * \attention
 * \param[in]   gate_T，网关路由表，   gate_iterate回调函数f(void*);打印后可以看到节点名称；
 *
 * \retval  　<0,失败，=0成功；
 */

int gateway_node_iterate(gate_T, gate_iterate f);




/**
 * \brief 模糊查查，可以统统节点名称前面的字符查找相关的节点名称，通过回调函数返回
 * \attention
 * \param[in]   gate_T，网关路由表， node_name 节点名称  gate_iterate回调函数f(void*);打印后可以看到节点名称；
 *
 * \retval  　<0,失败，=0成功；
 */

int gateway_query_iterate(gate_T,char *node_name, gate_iterate f);


/**
 * \brief 节点发送数据网关路由器
 * \attention
 * \param[in]   gate_T，网关路由表，   node_name节点名称 ，data 节点要发送的数据，len数据长度
 *
 * \retval  　<0,失败，=0成功；
 */
int node_send_data_to_gateway(gate_T  gway, char *node_name, char *data, int len);


/**
 * \brief 节点从网关路由器读取数据
 * \attention
 * \param[in]   gate_T，网关路由表，   node_name节点名称 ，data 接收数据指针地址，len数据长度
 *
 * \retval  　GATE_OK<0,失败，GATE_OK=0成功；
 */
int node_read_data_from_gateway(gate_T  gway, char *node_name, char **data, int*len);


/**
 * \brief 网关路由器使用的通信协议，目前支持MQTT，HTTP协议,初始化完成会注册所有节点到云端
 * \attention
 * \param[in]   gate_T，网关路由表，   Protocol协议名称  param 为云端初始化参数ParamInit函数的返回值
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_route_init(gate_T  gway, char *ProtocolName,void *Protocolparam,gate_mqtt_msgArrived message);

/**
 * \brief 网关路由器读数据，目前支持MQTT
 * \attention
 * \param[in]   gate_T，网关路由表，   Protocol协议名称  param 为云端初始化参数ParamInit函数的返回值
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_route_read(gate_T  gway,int timeout_ms);


/**
 * \brief 网关写数据到服务器
 * \attention
 * \param[in]   gate_T，网关路由表
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_route_write(gate_T  gway,char *node_name);



/**
 * \brief 网关接收到数据进行分流
 * \attention
 * \param[in]   gate_T，网关路由表
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_route_flow_node(gate_T gt,char *data,int len,char* topic,int topiclen) ;

/**
 * \brief 关闭路由通讯功能
 * \attention
 * \param[in]   gate_T，网关路由表
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_route_close(gate_T  gway);




/**
 * \brief 注册所有节点设备到云端去,该函数建议放置到一个线程中去执行，先遍历出节点，然后一个一个去注册
 * \attention
 * \param[in]   gate_T，网关路由表
 *
 * \retval  　<0,失败，=0成功；
 */
int gateway_register_node_to_cloud(gate_T  gt, char* key,char*v, char* out) ;

```
