import time
import math
import random
from enum import Enum

class DogState(Enum):
    IDLE = "idle"
    WALKING = "walking"
    RUNNING = "running"
    TURNING = "turning"
    AVOIDING = "avoiding"
    SITTING = "sitting"
    STANDING = "standing"

class RobotDog:
    def __init__(self, name="RoboDog"):
        self.name = name
        self.state = DogState.IDLE
        self.position = {"x": 0, "y": 0}
        self.direction = 0  # 0-360度
        self.speed = 0
        self.is_obstacle_detected = False
        self.battery_level = 100
        
        # 传感器数据
        self.sensors = {
            "front_distance": 100,
            "left_distance": 100,
            "right_distance": 100,
            "gyro": {"x": 0, "y": 0, "z": 0}
        }
        
        # 腿部关节角度 (4条腿，每条腿3个关节)
        self.leg_joints = {
            "front_left": {"shoulder": 0, "elbow": 0, "wrist": 0},
            "front_right": {"shoulder": 0, "elbow": 0, "wrist": 0},
            "back_left": {"shoulder": 0, "elbow": 0, "wrist": 0},
            "back_right": {"shoulder": 0, "elbow": 0, "wrist": 0}
        }
        
        print(f"🐕 {self.name} 已初始化！")

    def update_sensors(self):
        """模拟传感器数据更新"""
        # 模拟超声波传感器检测障碍物
        self.sensors["front_distance"] = random.uniform(10, 200)
        self.sensors["left_distance"] = random.uniform(10, 200)
        self.sensors["right_distance"] = random.uniform(10, 200)
        
        # 检测前方是否有障碍物
        self.is_obstacle_detected = self.sensors["front_distance"] < 30

    def set_leg_position(self, leg, shoulder, elbow, wrist):
        """设置腿部关节角度"""
        if leg in self.leg_joints:
            self.leg_joints[leg]["shoulder"] = shoulder
            self.leg_joints[leg]["elbow"] = elbow
            self.leg_joints[leg]["wrist"] = wrist

    def walk_gait(self, step_count=1):
        """行走步态控制"""
        print("🚶 执行行走步态...")
        
        for step in range(step_count):
            # 步态序列：对角线腿同时移动
            # 第一相：左前腿和右后腿抬起
            self.set_leg_position("front_left", 20, -45, 30)
            self.set_leg_position("back_right", 20, -45, 30)
            time.sleep(0.2)
            
            # 第二相：左前腿和右后腿放下，右前腿和左后腿抬起
            self.set_leg_position("front_left", 0, -20, 0)
            self.set_leg_position("back_right", 0, -20, 0)
            self.set_leg_position("front_right", 20, -45, 30)
            self.set_leg_position("back_left", 20, -45, 30)
            time.sleep(0.2)
            
            # 第三相：所有腿回到中立位置
            for leg in self.leg_joints:
                self.set_leg_position(leg, 0, -20, 0)
            time.sleep(0.1)

    def move_forward(self, distance=1.0, speed=1.0):
        """向前移动"""
        self.state = DogState.WALKING
        self.speed = speed
        
        print(f"⬆️ 向前移动 {distance} 米，速度: {speed}")
        
        steps = int(distance * 2)  # 每步约0.5米
        self.walk_gait(steps)
        
        # 更新位置
        self.position["x"] += distance * math.cos(math.radians(self.direction))
        self.position["y"] += distance * math.sin(math.radians(self.direction))
        
        self.state = DogState.IDLE

    def turn_left(self, angle=45):
        """左转"""
        self.state = DogState.TURNING
        print(f"↺ 左转 {angle} 度")
        
        # 转弯步态：右侧腿步幅更大
        for _ in range(int(angle / 15)):
            self.set_leg_position("front_right", 25, -50, 35)
            self.set_leg_position("back_right", 25, -50, 35)
            self.set_leg_position("front_left", 15, -40, 25)
            self.set_leg_position("back_left", 15, -40, 25)
            time.sleep(0.3)
            
            # 回到中立位置
            for leg in self.leg_joints:
                self.set_leg_position(leg, 0, -20, 0)
            time.sleep(0.2)
        
        self.direction = (self.direction - angle) % 360
        self.state = DogState.IDLE

    def turn_right(self, angle=45):
        """右转"""
        self.state = DogState.TURNING
        print(f"↻ 右转 {angle} 度")
        
        # 转弯步态：左侧腿步幅更大
        for _ in range(int(angle / 15)):
            self.set_leg_position("front_left", 25, -50, 35)
            self.set_leg_position("back_left", 25, -50, 35)
            self.set_leg_position("front_right", 15, -40, 25)
            self.set_leg_position("back_right", 15, -40, 25)
            time.sleep(0.3)
            
            # 回到中立位置
            for leg in self.leg_joints:
                self.set_leg_position(leg, 0, -20, 0)
            time.sleep(0.2)
        
        self.direction = (self.direction + angle) % 360
        self.state = DogState.IDLE

    def avoid_obstacle(self):
        """避障算法"""
        self.state = DogState.AVOIDING
        print("⚠️ 检测到障碍物，执行避障...")
        
        # 检查左右距离，选择较宽的方向
        if self.sensors["left_distance"] > self.sensors["right_distance"]:
            print("向左避障")
            self.turn_left(60)
            self.move_forward(0.5, 0.8)
            self.turn_right(60)
        else:
            print("向右避障")
            self.turn_right(60)
            self.move_forward(0.5, 0.8)
            self.turn_left(60)
        
        self.state = DogState.IDLE

    def sit(self):
        """坐下动作"""
        self.state = DogState.SITTING
        print("🪑 坐下")
        
        # 后腿弯曲，前腿保持直立
        self.set_leg_position("back_left", 0, -90, 45)
        self.set_leg_position("back_right", 0, -90, 45)
        self.set_leg_position("front_left", 0, -10, 0)
        self.set_leg_position("front_right", 0, -10, 0)
        time.sleep(1)

    def stand(self):
        """站立动作"""
        self.state = DogState.STANDING
        print("🧍 站立")
        
        # 所有腿回到站立位置
        for leg in self.leg_joints:
            self.set_leg_position(leg, 0, -20, 0)
        time.sleep(0.5)
        
        self.state = DogState.IDLE

    def shake_hands(self):
        """握手动作"""
        print("🤝 握手")
        
        # 抬起右前腿
        self.set_leg_position("front_right", 45, -30, -20)
        time.sleep(2)
        
        # 摇摆几次
        for _ in range(3):
            self.set_leg_position("front_right", 50, -25, -15)
            time.sleep(0.3)
            self.set_leg_position("front_right", 40, -35, -25)
            time.sleep(0.3)
        
        # 放下腿
        self.set_leg_position("front_right", 0, -20, 0)

    def autonomous_navigation(self, target_x, target_y):
        """自主导航到目标点"""
        print(f"🎯 自主导航到目标点 ({target_x}, {target_y})")
        
        while True:
            self.update_sensors()
            
            # 计算到目标的距离和角度
            dx = target_x - self.position["x"]
            dy = target_y - self.position["y"]
            distance = math.sqrt(dx**2 + dy**2)
            target_angle = math.degrees(math.atan2(dy, dx))
            
            # 如果接近目标，停止
            if distance < 0.5:
                print("✅ 到达目标点！")
                break
            
            # 如果检测到障碍物，执行避障
            if self.is_obstacle_detected:
                self.avoid_obstacle()
                continue
            
            # 计算需要转向的角度
            angle_diff = (target_angle - self.direction + 180) % 360 - 180
            
            # 如果角度差异较大，先转向
            if abs(angle_diff) > 15:
                if angle_diff > 0:
                    self.turn_left(min(45, abs(angle_diff)))
                else:
                    self.turn_right(min(45, abs(angle_diff)))
            else:
                # 向前移动
                move_distance = min(1.0, distance)
                self.move_forward(move_distance)
            
            # 打印当前状态
            print(f"位置: ({self.position['x']:.1f}, {self.position['y']:.1f}), "
                  f"方向: {self.direction:.0f}°, 距离目标: {distance:.1f}m")
            
            time.sleep(0.5)

    def get_status(self):
        """获取机械狗状态"""
        return {
            "name": self.name,
            "state": self.state.value,
            "position": self.position,
            "direction": self.direction,
            "battery": self.battery_level,
            "sensors": self.sensors
        }

def demo():
    """演示程序"""
    print("=== 机械狗控制系统演示 ===\n")
    
    # 创建机械狗实例
    dog = RobotDog("小白")
    
    print("\n1. 基本动作演示:")
    dog.stand()
    time.sleep(1)
    dog.sit()
    time.sleep(1)
    dog.stand()
    
    print("\n2. 移动演示:")
    dog.move_forward(2.0, 1.0)
    dog.turn_left(90)
    dog.move_forward(1.0, 0.8)
    dog.turn_right(45)
    
    print("\n3. 特殊动作演示:")
    dog.shake_hands()
    
    print("\n4. 避障演示:")
    dog.update_sensors()
    dog.sensors["front_distance"] = 20  # 模拟前方有障碍物
    dog.is_obstacle_detected = True
    dog.avoid_obstacle()
    
    print("\n5. 自主导航演示:")
    dog.autonomous_navigation(5, 5)
    
    print("\n6. 最终状态:")
    status = dog.get_status()
    for key, value in status.items():
        print(f"{key}: {value}")

if __name__ == "__main__":
    demo()
