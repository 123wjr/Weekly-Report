<template>
    <div>
        <!-- 部门信息 -->
        <el-container>
            <el-container style="height: 700px; border: 1px solid #eee">
                <el-header
                    style="font-size:40px; background-color: rgb(238, 241, 246)"
                >tlias 宝可梦学习辅助系统</el-header>

                <el-container>
                    <el-aside width="230px" style="border: 1px solid #eee">
                        <el-menu :default-openeds="['1', '3']">
                            <el-submenu index="1">
                                <template slot="title">
                                    <i class="el-icon-message"></i>系统信息管理
                                </template>
                                <el-menu-item index="1-1">
                                    <router-link to="/dept">部门管理</router-link>
                                </el-menu-item>
                                <el-menu-item index="1-2">
                                    <router-link to="/emp">员工管理</router-link>
                                </el-menu-item>
                            </el-submenu>
                        </el-menu>
                    </el-aside>
                </el-container>
            </el-container>
        </el-container>
    </div>
</template>

<script>
export default {};
</script>

<style>
</style>