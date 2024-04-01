ssc相关代码结构

ssc部分
p_bp_server_->Start();
EudmPlannerServer::Start() 
void EudmPlannerServer::MainThread() 
PlanCycleCallback
RunOnce
ConstructSscMap
FillDynamicPart


本代码实际运行部分
p_traj_server_ = new plan_utils::TrajPlannerServer(nh, traj_planner_work_rate, ego_id);
TrajPlannerServer::TrajPlannerServer 构造函数自然调用
trajplan==RunOnceParking--->getKinoPath---> kino_path_finder_->search


p_traj_server_ ->start()
TrajPlannerServer::Start
--->TrajPlannerServer::MainThread()  
--->TrajPlannerServer::PlanCycleCallback() 循环
--->Replan--->trajplan


ssc地图更新
rajPlannerServer::Start
--->TrajPlannerServer::MainThread()  
--->TrajPlannerServer::PlanCycleCallback() 循环
里面取出最新一个地图

//尝试从 p_input_smm_buff_（可能是一个队列或缓冲区）中取出数据，并将取出的数据赋值给 last_smm_
    while (p_input_smm_buff_->try_dequeue(last_smm_)) {
      is_map_updated_ = true;
    }
定义 moodycamel::ReaderWriterQueue<SemanticMapManager> *p_input_smm_buff_;

p_input_smm_buff_在TrajPlannerServer::TrajPlannerServer构造时 初始化
    p_input_smm_buff_ = new moodycamel::ReaderWriterQueue<SemanticMapManager>(
        config_.kInputBufferSize);

p_input_smm_buff_ push时间
void TrajPlannerServer::PushSemanticMap(const SemanticMapManager& smm)  函数里
由SemanticMapUpdateCallback  &&  BehaviorUpdateCallback   （park.cc）  反的

先看BehaviorUpdateCallback，bind  private_callback_fn_（behavior_server_ros.cc）
p_bp_server_->BindBehaviorUpdateCallback(BehaviorUpdateCallback);
    private_callback_fn_=BehaviorUpdateCallback   （behavior_server_ros.cc）

behaviorPlannerServer::PlanCycleCallback()里出现private_callback_fn_(smm)  循环
实际完成的是 BehaviorUpdateCallback  PushSemanticMap
int BehaviorUpdateCallback(
    const semantic_map_manager::SemanticMapManager& smm) {
  p_smm_vis_->VisualizeDataWithStamp(ros::Time::now(),smm);
  p_smm_vis_->SendTfWithStamp(ros::Time::now(), smm);
  p_traj_server_->SetEgoState(smm.ego_vehicle().state());
  if (p_traj_server_) p_traj_server_->PushSemanticMap(smm);
  return 0;
}
void BehaviorPlannerServer::PushSemanticMap(const SemanticMapManager& smm) {
SemanticMapUpdateCallback 里PushSemanticMap


//this!
  semantic_map_manager::SemanticMapManager semantic_map_manager(
      ego_id, agent_config_path);//store the state information
  semantic_map_manager::RosAdapter smm_ros_adapter(nh, &semantic_map_manager);//connect with ros
  smm_ros_adapter.BindMapUpdateCallback(SemanticMapUpdateCallback); //connenction with phy simulato

private_callback_fn_==SemanticMapUpdateCallback  （ros_adapter.cc）
private_callback_fn_(*p_smm_)存入

p_smm_
 p_smm_ = ptr_smm==p_semantic_map_manager_
    p_data_renderer_ = new DataRenderer(ptr_smm);

总结 读取地图信息
更新p_smm_ = ptr_smm==p_semantic_map_manager_   存入   p_input_smm_buff_
PlanCycleCallback() 里读取最新一个地图
a*规划

