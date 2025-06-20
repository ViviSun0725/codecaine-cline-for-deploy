<template>
	<div class="flex justify-center items-start min-h-screen bg py-24 px-4">
		<div class="w-full max-w-md bg-white rounded-xl shadow-lg p-8">
			<!-- Google 登入 -->
			<form class="mb-4">
				<input type="hidden" name="authenticity_token" value="..." />
				<button
					type="button"
					@click="() => socialSignIn(new GoogleAuthProvider())"
					class="w-full flex items-center justify-center gap-2 bg-gray-700 text-white font-bold py-3 rounded-md cursor-pointer hover:bg-black transition duration-200"
				>
					<img
						src="https://img.icons8.com/color/16/000000/google-logo.png"
						alt="Google logo"
					/>
					<span>Sign Up with Google</span>
				</button>
				<button
					type="button"
					@click="() => socialSignIn(new GithubAuthProvider())"
					class="w-full flex items-center justify-center gap-2 bg-gray-700 text-white font-bold py-3 rounded-md cursor-pointer hover:bg-black transition duration-200 mt-3"
				>
					<img
						src="https://img.icons8.com/ios-glyphs/24/ffffff/github.png"
						alt="GitHub logo"
					/>
					<span>Sign Up with GitHub</span>
				</button>
			</form>

			<!-- 分隔文字 -->
			<p class="text-center text-gray-600 font-bold my-4">Or,</p>

			<!-- Email Sign Up 開關 -->
			<button
				@click="showEmailForm = !showEmailForm"
				class="w-full bg-gray-700 text-white font-bold py-3 rounded-md mb-4 cursor-pointer hover:bg-black transition duration-200"
			>
				Sign Up with Email
			</button>

			<!-- Email 表單 -->
			<transition
				@before-enter="beforeEnter"
				@enter="enter"
				@leave="leave"
				:css="false"
			>
				<div v-show="showEmailForm" ref="emailSection">
					<form @submit.prevent="register">
						<input type="hidden" value="..." />

						<div class="mb-4">
							<label
								for="email"
								class="block text-sm font-bold text-gray-700 mb-1"
								>Email</label
							>
							<input
								v-model="email"
								type="email"
								id="email"
								required
								autocomplete="email"
								maxlength="20"
								class="w-full border border-gray-300 rounded px-3 py-2 text-black bg-gray-200 hover:bg-white transition"
							/>
						</div>

						<div class="mb-4">
							<label
								for="password"
								class="block text-sm font-bold text-gray-700 mb-1"
								>Choose Password</label
							>
							<input
								v-model="password"
								id="password"
								type="password"
								autocomplete="new-password"
								required
								class="w-full border border-gray-300 rounded px-3 py-2 text-black bg-gray-200 hover:bg-white transition"
							/>
							<!-- 密碼驗證提示之後要改 -->
							<ul class="text-xs text-gray-600 mt-2 list-disc pl-5">
								<li>Include an <strong>UPPER</strong> and lowercase letter</li>
								<li>Include a number</li>
								<li>
									Include special characters: <code>@$!%*#?&><()^+_-</code>
								</li>
								<li>8–100 characters</li>
							</ul>
						</div>

						<button
							type="submit"
							class="w-full bg-green-500 text-white font-bold py-3 rounded-md mt-4 cursor-pointer hover:bg-gray-600 transition duration-200"
						>
							Submit
						</button>
					</form>
				</div>
			</transition>
		</div>
	</div>
</template>

<script setup>
import { ref } from "vue";
import { auth } from "../config/firebase";
import { useRouter } from "vue-router";
//google登入的部分
import {
	GithubAuthProvider,
	GoogleAuthProvider,
	signInWithPopup,
	createUserWithEmailAndPassword,
} from "firebase/auth";
import { useAuthStore } from "../stores/useAuthStore";
import api from "../config/api"; // 假設有一個 api.js 檔案處理 API 請求

const authStore = useAuthStore();
const router = useRouter();

const showEmailForm = ref(false);
const email = ref("");
const password = ref("");
const error = ref("");
const success = ref("");

const register = async () => {
	error.value = "";
	success.value = "";

	try {
		const userCredential = await createUserWithEmailAndPassword(
			auth,
			email.value,
			password.value
		);
		success.value = "註冊成功！";
		alert(success.value); //alert最後可以再調整美觀的樣式
		router.push("/login");
		return;
	} catch (e) {
		let msg = "";
		switch (e.code) {
			case "auth/email-already-in-use":
				msg = "這個 Email 已經被註冊了";
				break;
			case "auth/invalid-email":
				msg = "Email 格式不正確";
				break;
			case "auth/password-does-not-meet-requirements":
				msg = "密碼太弱，請使用至少 8 個字元";
				break;
			default:
				msg = "註冊失敗：" + e.message;
		}
		alert(msg);
	}
};

// Google 登入函式
const socialSignIn = async (provider) => {
	try {
		const result = await signInWithPopup(auth, provider);
		const user = result.user;
		const token = await user.getIdToken();
		authStore.setToken(token);
		await api.get("/api/auth/me");
		alert(
			`${
				provider.providerId.includes("google") ? "Google" : "GitHub"
			} 登入成功！`
		);
		router.push("/trending");
	} catch (error) {
		if (error.code === "auth/account-exists-with-different-credential") {
			alert(
				"This email is already registered with another sign-in method. Please use the original method to log in."
			);
		}
		console.error(`${provider.providerId} 登入錯誤:`, error);
		alert(
			`${provider.providerId.includes("google") ? "Google" : "GitHub"} 登入失敗`
		);
	}
};

// 顯示與動畫
function beforeEnter(el) {
	el.style.height = "0";
	el.style.opacity = "0";
}

function enter(el, done) {
	el.style.transition = "all 0.3s ease";
	const height = el.scrollHeight;
	el.style.height = height + "px";
	el.style.opacity = "1";
	setTimeout(() => {
		el.style.height = "auto";
		done();
	}, 300);
}

function leave(el, done) {
	el.style.transition = "all 0.3s ease";
	el.style.height = el.scrollHeight + "px";
	requestAnimationFrame(() => {
		el.style.height = "0";
		el.style.opacity = "0";
	});
	setTimeout(done, 300);
}
</script>

<style scoped>
.bg {
	background-image: url("https://cpwebassets.codepen.io/assets/logos/codepen-logo-pattern-b477875ac66ffc21e4485a989358c220fac283caf17e602346a50d4250970254.png");
}
</style>
