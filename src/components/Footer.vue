<script setup>
import { ref, onMounted, computed } from 'vue'
import instance from "@/utils/request";
import { useCookies } from "vue3-cookies";
import { ElMessage } from "element-plus";
import { useRouter } from 'vue-router'

const router = useRouter()
const { cookies } = useCookies();

const year = ref(0);
const logined = ref(false);
const adminLoginDialogVisible = ref(false);
const adminLoginReq = ref({
    username: "",
    password: "",
})

function getYear() {
    var date = new Date();
    year.value = date.getFullYear();
}

function openAdminLoginDialog() {
    adminLoginDialogVisible.value = true;
}

function closeAdminLoginDialog() {
    adminLoginDialogVisible.value = false;
}

function doAdminLoginReq() {
    var api = "/auth/login";
    var params = {
        "username": adminLoginReq.value.username,
        "password": adminLoginReq.value.password,
    }

    instance.post(api, params).then((res) => {
        console.log(res)
        if (res["code"] == 200) {
            ElMessage.success("login success!!!");
            checkLogined();
        }
    }).catch((err) => {
        console.log(err)
    })
    adminLoginDialogVisible.value = false;
}

function doAdminLogoutReq() {
    var api = "/auth/logout";
    var params = {}

    instance.post(api, params).then((res) => {
        console.log(res)
        if (res["code"] == 200) {
            cookies.remove('SESSION_ID');
            checkLogined();
            ElMessage.success("logout success!!!")
        }
    }).catch((err) => {
        console.log(err)
    })
}

function checkLogined() {
    let session = cookies.get("SESSION_ID");
    logined.value = (session && session != "");
    console.log(session);
}

function goNewArticle() {
    router.push("/editor/0")
}

onMounted(() => {
    getYear();
    checkLogined();
})

</script>

<template>
    <footer class="footer">
        <div class="footer-container">
            <!-- 主要内容区域 -->
            <div class="footer-main">
                <!-- 品牌和链接区域 -->
                <div class="footer-content">
                    <div class="brand-section">
                        <h3 class="brand-name">JuneBao</h3>
                        <div class="all-links">
                            <el-link :underline="false" href="https://github.com/520MianXiangDuiXiang520" target="_blank" class="footer-link">
                                GitHub
                            </el-link>
                            <span class="separator">|</span>
                            <el-link :underline="false" href="https://blog.csdn.net/zjbyough" target="_blank" class="footer-link">
                                CSDN
                            </el-link>
                            <span class="separator">|</span>
                            <el-link :underline="false" href="mailto:15364968962@163.com" class="footer-link">
                                Email
                            </el-link>
                            <span class="separator">|</span>
                            <el-link :underline="false" href="/sitemap.xml" class="footer-link">
                                SiteMap
                            </el-link>
                            
                            <template v-if="!logined">
                                <span class="separator">|</span>
                                <el-link :underline="false" @click="openAdminLoginDialog()" class="footer-link">
                                    AdminLogin
                                </el-link>
                            </template>
                            
                            <template v-else>
                                <span class="separator">|</span>
                                <el-link :underline="false" @click="doAdminLogoutReq()" class="footer-link">
                                    Logout
                                </el-link>
                                <span class="separator">|</span>
                                <el-link :underline="false" @click="goNewArticle()" class="footer-link">
                                    NewArticle
                                </el-link>
                            </template>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 底部信息区域 -->
            <div class="footer-bottom">
                <div class="bottom-content">
                    <div class="copyright-info">
                        <span class="copyright-text">© 2019-{{ year }} JuneBao</span>
                    </div>
                    <div class="beian-info">
                        <div class="beian-item">
                            <el-link :underline="false" href="https://beian.miit.gov.cn/" target="_blank" class="beian-link">
                                陇ICP备20000438号-1
                            </el-link>
                        </div>
                        <div class="beian-item gongan-beian">
                            <img src="/beian.png" alt="公安备案图标" class="beian-icon" />
                            <el-link :underline="false" href="https://beian.mps.gov.cn/#/query/webSearch?code=51010702043658" rel="noreferrer" target="_blank" class="beian-link">
                                川公网安备51010702043658号
                            </el-link>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </footer>

    <!-- 管理员登录对话框 -->
    <el-dialog v-model="adminLoginDialogVisible" title="AdminLogin" width="400" :close-on-click-modal="false">
        <el-form>
            <el-form-item label="UserName">
                <el-input v-model="adminLoginReq.username" autocomplete="off" />
            </el-form-item>
            <el-form-item label="Password">
                <el-input v-model="adminLoginReq.password" type="password" placeholder="Please input password"
                    show-password autocomplete="off" />
            </el-form-item>
        </el-form>
        <template #footer>
            <div class="dialog-footer">
                <el-button @click="closeAdminLoginDialog()">Cancel</el-button>
                <el-button type="primary" @click="doAdminLoginReq()">
                    Login
                </el-button>
            </div>
        </template>
    </el-dialog>
</template>

<style scoped>
.footer {
    margin-top: 40px;
    background-color: var(--bPageBgColor);
    border-top: 1px solid var(--menuBorderColor);
    color: var(--bTextColor);
    position: relative;
}

.footer-container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 20px;
}

.footer-main {
    padding: 30px 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 20px;
}

.brand-section {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 15px;
}

.brand-name {
    font-size: 22px;
    font-weight: 700;
    margin: 0;
    color: var(--titleColor);
}

.all-links {
    display: flex;
    align-items: center;
    gap: 12px;
    flex-wrap: wrap;
    justify-content: center;
}

.separator {
    color: var(--bTextColor);
    opacity: 0.4;
    font-size: 11px;
    margin: 0 2px;
}

/* 底部区域 */
.footer-bottom {
    padding: 0;
    background-color: transparent;
}

.bottom-content {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    gap: 20px;
}

.copyright-info {
    order: 2;
}

.copyright-text {
    font-size: 12px;
    color: var(--bTextColor);
    opacity: 0.7;
}

.beian-info {
    display: flex;
    gap: 15px;
    align-items: center;
    order: 1;
}

.footer-link {
    color: var(--bTextColor) !important;
    font-size: 13px;
    text-decoration: none;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    padding: 2px 0;
    position: relative;
    opacity: 0.8;
}

.footer-link:hover {
    color: var(--titleColor) !important;
    opacity: 1;
    transform: translateX(3px);
}

.beian-item {
    display: flex;
    align-items: center;
    gap: 4px;
}

.gongan-beian {
    gap: 6px;
}

.beian-link {
    color: var(--bTextColor) !important;
    font-size: 12px;
    text-decoration: none;
    transition: color 0.3s ease;
    opacity: 0.8;
}

.beian-link:hover {
    color: var(--titleColor) !important;
    opacity: 1;
}

.beian-icon {
    width: 14px;
    height: 14px;
    opacity: 0.8;
}


/* 响应式设计 */
@media (max-width: 768px) {
    .footer-main {
        padding: 25px 0;
        gap: 15px;
    }
    
    .brand-section {
        gap: 12px;
    }
    
    .all-links {
        gap: 8px;
    }
    
    .brand-name {
        font-size: 20px;
    }
    
    .bottom-content {
        flex-direction: column;
        text-align: center;
        gap: 12px;
    }
    
    .beian-info {
        flex-direction: column;
        gap: 8px;
        order: 1;
    }
    
    .copyright-info {
        order: 2;
    }
}

@media (max-width: 480px) {
    .footer-container {
        padding: 0 15px;
    }
    
    .footer-main {
        padding: 20px 0;
        gap: 12px;
    }
    
    .brand-name {
        font-size: 18px;
    }
    
    .all-links {
        gap: 6px;
    }
    
    .footer-link {
        font-size: 12px;
    }
    
    .bottom-content {
        gap: 10px;
    }
}

/* 动画效果 */
@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translateY(10px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

.footer-main > * {
    animation: fadeInUp 0.4s ease-out;
}

.footer-main > *:nth-child(2) {
    animation-delay: 0.05s;
}

.footer-main > *:nth-child(3) {
    animation-delay: 0.1s;
}
</style>