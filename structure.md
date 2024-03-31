ssc相关代码结构

ssc部分
p_bp_server_->Start();
EudmPlannerServer::Start() 
void EudmPlannerServer::MainThread() {
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
