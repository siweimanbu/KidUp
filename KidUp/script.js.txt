// ===================== 配置项（可自由修改） =====================
const PASS_SCORE = 60; // 及格分数（低于这个分重新学习）
const EXAM_TOTAL = 10; // 考核总题数

// 三大模块知识点（3-6岁基础内容）
const subjectData = {
    math: {
        name: "数学启蒙",
        study: "1. 认识数字1-10\n2. 简单加减法：1+1=2，2+1=3\n3. 认识圆形、正方形、三角形",
        game: "🎮 游戏：点击正确的数字！\n请问：2的后面是数字几？",
        exam: [
            {q: "1+1=？", a: ["1", "2", "3"], correct: 1},
            {q: "5-2=？", a: ["2", "3", "4"], correct: 1},
            {q: "圆形是什么样子？", a: ["方方", "圆圆", "尖尖"], correct: 1}
        ]
    },
    english: {
        name: "英语字母",
        study: "1. 认识字母A-G\n2. A=apple（苹果），B=ball（球）\n3. 跟读字母发音",
        game: "🎮 游戏：找到字母A！\n点击图片中的字母A",
        exam: [
            {q: "苹果的英语是？", a: ["apple", "ball", "cat"], correct: 0},
            {q: "字母B的发音是？", a: ["ei", "bi", "si"], correct: 1},
            {q: "A的后面是？", a: ["C", "B", "D"], correct: 1}
        ]
    },
    pinyin: {
        name: "拼音识字",
        study: "1. 认识声母：b p m f\n2. 认识单韵母：a o e i u ü\n3. 拼读：ba=爸，ma=妈",
        game: "🎮 游戏：找到声母b！\n点击正确的拼音",
        exam: [
            {q: "爸爸的拼音是？", a: ["ma", "ba", "pa"], correct: 1},
            {q: "单韵母a的发音是？", a: ["啊", "哦", "鹅"], correct: 0},
            {q: "b的后面是？", a: ["p", "m", "f"], correct: 0}
        ]
    }
};

// 全局变量
let currentSubject = "math"; // 默认选中数学
let examScore = 0;
let examIndex = 0;

// ===================== 核心功能 =====================
// 1. 切换学习分类（数学/英语/拼音）
function switchSubject(sub) {
    currentSubject = sub;
    // 重置所有模块
    document.querySelectorAll(".nav-btn").forEach(btn => btn.classList.remove("active"));
    event.target.classList.add("active");
    showStudy();
}

// 2. 显示学习模块
function showStudy() {
    hideAllMode();
    document.getElementById("study").classList.add("show");
    const content = subjectData[currentSubject].study;
    document.getElementById("study-content").innerText = content;
    // 自动语音朗读（幼儿友好）
    speak(content);
}

// 3. 开始游戏
function startGame() {
    hideAllMode();
    document.getElementById("game").classList.add("show");
    const content = subjectData[currentSubject].game;
    document.getElementById("game-content").innerText = content;
    speak(content);
}

// 4. 开始考核
function startExam() {
    hideAllMode();
    document.getElementById("exam").classList.add("show");
    examScore = 0;
    examIndex = 0;
    document.getElementById("score").innerText = 0;
    document.getElementById("restart-btn").style.display = "none";
    showExamQuestion();
}

// 显示考核题目
function showExamQuestion() {
    const exam = subjectData[currentSubject].exam;
    if (examIndex >= exam.length) {
        // 考核结束，判定分数
        finishExam();
        return;
    }
    const q = exam[examIndex];
    let html = `<p>${q.q}</p>`;
    q.a.forEach((item, i) => {
        html += `<button onclick="checkAnswer(${i}, ${q.correct})" class="big-btn">${item}</button>`;
    });
    document.getElementById("exam-content").innerHTML = html;
}

// 核对答案
function checkAnswer(select, correct) {
    if (select === correct) examScore += 10;
    document.getElementById("score").innerText = examScore;
    examIndex++;
    showExamQuestion();
}

// 考核结束
function finishExam() {
    let result = "";
    if (examScore >= PASS_SCORE) {
        result = "🎉 太棒了！闯关成功！";
        speak(result);
    } else {
        result = `😥 加油哦！得分${examScore}，需要重新学习~`;
        document.getElementById("restart-btn").style.display = "block";
        speak(result);
    }
    document.getElementById("exam-content").innerHTML = `<h3>${result}</h3>`;
}

// 重新学习（低分循环）
function restartStudy() {
    showStudy();
}

// 隐藏所有模块
function hideAllMode() {
    document.querySelectorAll(".mode-box").forEach(box => box.classList.remove("show"));
}

// 语音朗读（浏览器自带，无需音频文件）
function speak(text) {
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = "zh-CN";
    utterance.rate = 0.8; // 语速放慢，适合幼儿
    window.speechSynthesis.speak(utterance);
}

// 初始化：默认显示数学学习
window.onload = showStudy;