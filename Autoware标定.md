# 标定资料

## 安装Autoware

Ubuntu 16.04和Ubuntu18.04都可以使用Autoware v1.12.0，所以安装此版本

1. 创建文件夹

   ```shell
   $ mkdir -p autoware.ai/src
   $ cd autoware.ai
   ```

2. 下载源代码

   ```shell
   $ wget -O autoware.ai.repos "https://raw.githubusercontent.com/Autoware-AI/autoware.ai/1.12.0/autoware.ai.repos"
   ```

3. 将Autoware导入src文件夹

   ```shell
   $ vcs import src < autoware.ai.repos
   ```

4. 使用 `rosdep`安装

   ```shell
   $ rosdep update
   $ rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_发行版
   ```

5. 编译安装

   ```shell
   $ colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release
   ```