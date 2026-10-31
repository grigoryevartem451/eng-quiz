<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fill in the Blanks: English Quiz</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f0f4f8;
      color: #333;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 20px;
    }

    .quiz-card {
      background: #ffffff;
      width: 100%;
      max-width: 600px;
      padding: 30px;
      border-radius: 16px;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.08);
      text-align: center;
    }

    h1 {
      font-size: 24px;
      color: #1e293b;
      margin-bottom: 8px;
    }

    h2 {
      font-size: 15px;
      color: #64748b;
      font-weight: 400;
      margin-bottom: 24px;
    }

    .progress-bar {
      width: 100%;
      background-color: #e2e8f0;
      height: 8px;
      border-radius: 4px;
      margin-bottom: 20px;
      overflow: hidden;
    }

    .progress-fill {
      height: 100%;
      width: 0%;
      background-color: #3b82f6;
      transition: width 0.3s ease;
    }

    .status-text {
      font-size: 14px;
      font-weight: 600;
      color: #3b82f6;
      margin-bottom: 16px;
      text-align: right;
    }

    .question-box {
      background-color: #f8fafc;
      border: 2px solid #e2e8f0;
      border-radius: 12px;
      padding: 24px 20px;
      font-size: 18px;
      line-height: 1.6;
      color: #0f172a;
      margin-bottom: 24px;
      white-space: pre-line;
      text-align: left;
    }

    .options-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
      margin-bottom: 20px;
    }

    .btn-option {
      background-color: #ffffff;
      border: 2px solid #cbd5e1;
      border-radius: 10px;
      padding: 14px 16px;
      font-size: 16px;
      font-weight: 600;
      color: #334155;
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .btn-option:hover:not(:disabled) {
      border-color: #3b82f6;
      background-color: #eff6ff;
      color: #1d4ed8;
    }

    .btn-option:disabled {
      cursor: default;
    }

    .btn-option.correct {
      background-color: #dcfce7 !important;
      border-color: #22c55e !important;
      color: #15803d !important;
    }

    .btn-option.wrong {
      background-color: #fee2e2 !important;
      border-color: #ef4444 !important;
      color: #b91c1c !important;
    }

    .feedback-message {
      min-height: 28px;
      font-size: 18px;
      font-weight: 700;
    }

    .feedback-message.correct {
      color: #16a34a;
    }

    .feedback-message.wrong {
      color: #dc2626;
    }

    .result-box h3 {
      font-size: 26px;
      color: #0f172a;
      margin-bottom: 12px;
    }

    .result-box p {
      font-size: 18px;
      color: #475569;
      margin-bottom: 24px;
    }

    .btn-restart {
      background-color: #3b82f6;
      color: white;
      border: none;
      padding: 12px 28px;
      font-size: 16px;
      font-weight: 600;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    .btn-restart:hover {
      background-color: #2563eb;
    }

    @media (max-width: 480px) {
      .options-grid {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>
<body>

  <div class="quiz-card" id="quiz-card">
    <h1>Fill in the Blanks: Missing Words</h1>
    <h2>Choose the correct word to complete each dialogue</h2>

    <div class="progress-bar">
      <div class="progress-fill" id="progress-fill"></div>
    </div>
    
    <div class="status-text" id="status-text">Вопрос 1 из 5</div>

    <div class="question-box" id="question-text">
      Загрузка...
    </div>

    <div class="options-grid" id="options-container"></div>

    <div class="feedback-message" id="feedback"></div>
  </div>

  <script>
    // Массив с вопросами. Можно легко добавлять новые!
    const questions = [
      {
        question: "A: Where ___ you go yesterday?\nB: I went to the park.",
        options: ["do", "did", "are", "have"],
        correct: 1
      },
      {
        question: "A: She ___ like coffee, she prefers tea.\nB: Oh, I didn't know that.",
        options: ["don't", "isn't", "doesn't", "hasn't"],
        correct: 2
      },
      {
        question: "A: Have you ___ been to London?\nB: Yes, I went there last summer.",
        options: ["ever", "never", "already", "yet"],
        correct: 0
      },
      {
        question: "A: What are you doing right now?\nB: I ___ a book.",
        options: ["read", "am reading", "was reading", "have read"],
        correct: 1
      },
      {
        question: "A: If it rains tomorrow, we ___ stay at home.\nB: Good idea.",
        options: ["would", "will", "did", "are"],
        correct: 1
      }
    ];

    let currentIndex = 0;
    let score = 0;

    const questionElem = document.getElementById("question-text");
    const optionsContainer = document.getElementById("options-container");
    const statusText = document.getElementById("status-text");
    const progressFill = document.getElementById("progress-fill");
    const feedbackElem = document.getElementById("feedback");

    function renderQuestion() {
      const q = questions[currentIndex];
      
      // Обновляем прогресс
      statusText.innerText = `Вопрос ${currentIndex + 1} из ${questions.length}`;
      progressFill.style.width = `${((currentIndex + 1) / questions.length) * 100}%`;
      
      // Очищаем предыдущий ответ
      questionElem.innerText = q.question;
      optionsContainer.innerHTML = "";
      feedbackElem.innerText = "";
      feedbackElem.className = "feedback-message";

      // Создаем кнопки вариантов ответов
      q.options.forEach((optText, idx) => {
        const btn = document.createElement("button");
        btn.className = "btn-option";
        btn.innerText = optText;
        btn.onclick = () => selectAnswer(idx, btn);
        optionsContainer.appendChild(btn);
      });
    }

    function selectAnswer(selectedIndex, selectedBtn) {
      const q = questions[currentIndex];
      const allButtons = optionsContainer.querySelectorAll(".btn-option");

      // Блокируем все кнопки от повторных нажатий
      allButtons.forEach(btn => btn.disabled = true);

      if (selectedIndex === q.correct) {
        selectedBtn.classList.add("correct");
        feedbackElem.innerText = "Правильно! 🎉";
        feedbackElem.classList.add("correct");
        score++;
      } else {
        selectedBtn.classList.add("wrong");
        allButtons[q.correct].classList.add("correct");
        feedbackElem.innerText = "Неправильно ❌";
        feedbackElem.classList.add("wrong");
      }

      // Пауза 1.5 секунды перед следующим вопросом
      setTimeout(() => {
        currentIndex++;
        if (currentIndex < questions.length) {
          renderQuestion();
        } else {
          showFinalResults();
        }
      }, 1500);
    }

    function showFinalResults() {
      const card = document.getElementById("quiz-card");
      card.innerHTML = `
        <div class="result-box">
          <h3>Тест завершён!</h3>
          <p>Ваш результат: <strong>${score}</strong> из <strong>${questions.length}</strong></p>
          <button class="btn-restart" onclick="location.reload()">Пройти заново</button>
        </div>
      `;
    }

    // Старт работы
    renderQuestion();
  </script>
</body>
</html>
