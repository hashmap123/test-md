ypedef void* gate_T;
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
