# dreamer
dreamer
import React, { useState, useEffect } from 'react';

interface Question {
  question: string;
  options: string[];
  correctAnswer: number;
  explanation: string;
  difficulty: 'easy' | 'medium' | 'hard';
}

export default function FinancialQuizGame() {
  const [currentQuestion, setCurrentQuestion] = useState(0);
  const [score, setScore] = useState(0);
  const [selectedAnswer, setSelectedAnswer] = useState<number | null>(null);
  const [showExplanation, setShowExplanation] = useState(false);
  const [gameComplete, setGameComplete] = useState(false);
  const [timeLeft, setTimeLeft] = useState(15);
  const [strikes, setStrikes] = useState(0);
  const [timeoutReached, setTimeoutReached] = useState(false);

  const questions: Question[] = [
    {
      question: "What is the recommended emergency fund amount for a starter?",
      options: ["$500", "$1,000", "$5,000", "$10,000"],
      correctAnswer: 1,
      explanation: "A starter emergency fund of $1,000 is recommended before tackling debt or other financial goals.",
      difficulty: 'easy'
    },
    {
      question: "According to the 50/30/20 rule, what percentage should go to needs?",
      options: ["30%", "40%", "50%", "60%"],
      correctAnswer: 2,
      explanation: "The 50/30/20 rule allocates 50% for needs, 30% for wants, and 20% for savings and debt repayment.",
      difficulty: 'easy'
    },
    {
      question: "Which debt repayment strategy saves the most money in interest?",
      options: ["Snowball Method", "Avalanche Method", "Debt Consolidation", "Minimum Payment Method"],
      correctAnswer: 1,
      explanation: "The Avalanche Method prioritizes paying off the highest interest debt first, saving the most in total interest.",
      difficulty: 'medium'
    },
    {
      question: "What is a good credit utilization rate?",
      options: ["Under 10%", "Under 30%", "Under 50%", "Under 70%"],
      correctAnswer: 1,
      explanation: "Keeping your credit utilization below 30% helps maintain a good credit score and shows responsible credit usage.",
      difficulty: 'medium'
    },
    {
      question: "Which investment is best for beginners?",
      options: ["Individual Stocks", "Crypto", "Index Funds/ETFs", "Penny Stocks"],
      correctAnswer: 2,
      explanation: "Index funds and ETFs offer instant diversification, low costs, and are perfect for beginners.",
      difficulty: 'medium'
    },
    {
      question: "What is the highest credit score range?",
      options: ["650-700", "700-750", "750-800", "800-850"],
      correctAnswer: 3,
      explanation: "A credit score of 800-850 is considered 'Excellent' and qualifies for the best interest rates.",
      difficulty: 'easy'
    },
    {
      question: "When should you start investing?",
      options: ["Immediately", "After building an emergency fund", "When you're 30", "When you make $100k/year"],
      correctAnswer: 1,
      explanation: "Start investing after you have an emergency fund (3-6 months expenses) and have paid off high-interest debt.",
      difficulty: 'medium'
    },
    {
      question: "What is compound interest?",
      options: ["Interest on your initial investment only", "Earning interest on your interest", "Fixed interest rate", "Interest from banks only"],
      correctAnswer: 1,
      explanation: "Compound interest is earning interest on previously earned interest, allowing wealth to grow exponentially over time.",
      difficulty: 'medium'
    },
    {
      question: "Which is considered high-priority debt?",
      options: ["Mortgage", "Federal Student Loans", "Credit Card Debt", "Auto Loan"],
      correctAnswer: 2,
      explanation: "Credit card debt typically has 15-25% interest rates, making it high-priority debt that should be paid off first.",
      difficulty: 'hard'
    },
    {
      question: "What does SMART stand for in financial goal setting?",
      options: ["Simple, Meaningful, Achievable, Realistic, Timely", "Specific, Measurable, Achievable, Relevant, Time-bound", "Strategic, Measurable, Ambitious, Realistic, Timely", "Specific, Meaningful, Ambitious, Relevant, Timely"],
      correctAnswer: 1,
      explanation: "SMART goals are Specific, Measurable, Achievable, Relevant, and Time-bound.",
      difficulty: 'hard'
    },
    {
      question: "What is the Rule of 72 used for?",
      options: ["Calculating tax deductions", "Estimating time to double investment", "Determining credit score", "Budgeting monthly expenses"],
      correctAnswer: 1,
      explanation: "The Rule of 72 estimates how long it takes for an investment to double by dividing 72 by the interest rate.",
      difficulty: 'hard'
    },
    {
      question: "At what credit score range do lenders start to charge significantly higher rates?",
      options: ["Under 600", "Under 660", "Under 700", "Under 750"],
      correctAnswer: 1,
      explanation: "Credit scores below 660 are considered fair or poor, resulting in significantly higher interest rates from lenders.",
      difficulty: 'hard'
    },
    {
      question: "What is dollar-cost averaging in investing?",
      options: ["Investing $1,000 every time", "Buying stocks at $1 each", "Investing fixed amounts regularly regardless of price", "Selling when dollar is weak"],
      correctAnswer: 2,
      explanation: "Dollar-cost averaging is investing the same fixed amount regularly, which helps reduce the impact of market volatility.",
      difficulty: 'hard'
    },
    {
      question: "What percentage of payment history contributes to your credit score?",
      options: ["15%", "25%", "35%", "45%"],
      correctAnswer: 2,
      explanation: "Payment history is the single biggest factor in credit score, making up 35% of your total FICO score.",
      difficulty: 'hard'
    },
    {
      question: "What is a Roth IRA contribution limit for 2024 (under 50)?",
      options: ["$5,500", "$6,500", "$7,000", "$8,000"],
      correctAnswer: 2,
      explanation: "For 2024, the IRA contribution limit is $7,000 for those under 50 years old.",
      difficulty: 'hard'
    },
    {
      question: "What's the average credit card interest rate in the U.S.?",
      options: ["10-15%", "15-25%", "25-30%", "30-35%"],
      correctAnswer: 1,
      explanation: "The average credit card APR ranges from 15-25%, making credit card debt among the most expensive debt to carry.",
      difficulty: 'medium'
    },
    {
      question: "How much should your emergency fund cover if you're self-employed?",
      options: ["3 months", "6 months", "9 months", "12 months"],
      correctAnswer: 2,
      explanation: "Self-employed individuals should aim for 6-9 months of expenses due to irregular income patterns.",
      difficulty: 'hard'
    }
  ];

  // Timer effect
  useEffect(() => {
    if (gameComplete || showExplanation) return;
    
    const timer = setInterval(() => {
      setTimeLeft((prev) => {
        if (prev <= 1) {
          setTimeoutReached(true);
          return 0;
        }
        return prev - 1;
      });
    }, 1000);

    return () => clearInterval(timer);
  }, [currentQuestion, showExplanation, gameComplete]);

  // Reset timer on new question
  useEffect(() => {
    setTimeLeft(15);
    setTimeoutReached(false);
  }, [currentQuestion]);

  // Handle timeout
  useEffect(() => {
    if (timeoutReached && !showExplanation) {
      setStrikes(prev => prev + 1);
      setShowExplanation(true);
    }
  }, [timeoutReached, showExplanation]);

  const handleAnswerClick = (index: number) => {
    if (showExplanation || timeoutReached) return;
    setSelectedAnswer(index);
    
    const isCorrect = index === questions[currentQuestion].correctAnswer;
    
    if (isCorrect) {
      setScore(prev => prev + 1);
    } else {
      setStrikes(prev => prev + 1);
    }
    
    setShowExplanation(true);
  };

  const handleNextQuestion = () => {
    if (currentQuestion < questions.length - 1) {
      setCurrentQuestion(currentQuestion + 1);
      setSelectedAnswer(null);
      setShowExplanation(false);
    } else {
      setGameComplete(true);
    }
  };

  const handleRestart = () => {
    setCurrentQuestion(0);
    setScore(0);
    setSelectedAnswer(null);
    setShowExplanation(false);
    setGameComplete(false);
    setTimeLeft(15);
    setStrikes(0);
    setTimeoutReached(false);
  };

  const getDifficultyColor = (difficulty: string) => {
    if (difficulty === 'easy') return 'text-green-600 bg-green-50';
    if (difficulty === 'medium') return 'text-yellow-600 bg-yellow-50';
    return 'text-red-600 bg-red-50';
  };

  const getScoreColor = (percentage: number) => {
    if (percentage >= 80) return 'text-green-600';
    if (percentage >= 60) return 'text-yellow-600';
    return 'text-red-600';
  };

  const getScoreMessage = (percentage: number) => {
    if (percentage >= 90) return "🎉 Outstanding! You're a financial pro!";
    if (percentage >= 80) return "🌟 Excellent work! You know your finances!";
    if (percentage >= 70) return "👍 Good job! Keep learning!";
    if (percentage >= 60) return "📚 Not bad! Review and try again!";
    return "💪 Keep studying! Practice makes perfect!";
  };

  if (gameComplete) {
    const percentage = Math.round((score / questions.length) * 100);
    const incorrectAnswers = questions.length - score;
    const timeouts = strikes - incorrectAnswers;
    return (
      <div className="min-h-screen bg-gradient-to-br from-blue-100 to-purple-100 flex items-center justify-center p-4">
        <div className="bg-white rounded-lg shadow-2xl p-8 max-w-md w-full text-center">
          <h1 className="text-3xl font-bold mb-4">Quiz Complete! 🎊</h1>
          
          <div className="mb-6">
            <div className="text-6xl mb-4">{percentage >= 90 ? '🏆' : percentage >= 80 ? '⭐' : percentage >= 60 ? '📖' : '💪'}</div>
            <p className={`text-4xl font-bold mb-2 ${getScoreColor(percentage)}`}>
              {score} / {questions.length}
            </p>
            <p className="text-2xl font-semibold">{percentage}%</p>
          </div>

          <div className="bg-gray-100 rounded-lg p-4 mb-6">
            <p className="text-lg">{getScoreMessage(percentage)}</p>
          </div>

          <div className="bg-blue-100 rounded-lg p-4 mb-6 text-left">
            <p className="font-semibold mb-2">Detailed Results:</p>
            <p>✅ Correct: {score}</p>
            <p>❌ Wrong Answers: {incorrectAnswers}</p>
            <p>⏱️ Time Outs: {timeouts > 0 ? timeouts : 0}</p>
            <p>📊 Final Score: {percentage}%</p>
          </div>

          <button
            onClick={handleRestart}
            className="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg text-lg transition duration-200"
          >
            Play Again
          </button>
        </div>
      </div>
    );
  }

  const question = questions[currentQuestion];
  const progress = ((currentQuestion + 1) / questions.length) * 100;

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-100 to-purple-100 flex items-center justify-center p-4">
      <div className="bg-white rounded-lg shadow-2xl p-8 max-w-2xl w-full">
        {/* Header */}
        <div className="mb-6">
          <div className="flex justify-between items-center mb-4">
            <h1 className="text-3xl font-bold">💰 Financial Literacy Quiz</h1>
            <div className="flex items-center space-x-2">
              <span className="text-xs font-bold text-red-600">STRIKES:</span>
              <div className="flex space-x-1">
                {[...Array(3)].map((_, i) => (
                  <span key={i} className={i < strikes ? 'text-red-600' : 'text-gray-300'}>
                    ⚡
                  </span>
                ))}
              </div>
            </div>
          </div>

          <div className="flex justify-between items-center mb-3">
            <span className="text-sm font-semibold">Question {currentQuestion + 1} of {questions.length}</span>
            <span className="text-sm font-semibold text-blue-600">Score: {score} / {questions.length}</span>
          </div>

          {/* Timer */}
          <div className="flex items-center mb-2">
            <span className="text-xs font-semibold mr-2">⏱️ Time:</span>
            <div className="flex-1 bg-gray-200 rounded-full h-3">
              <div
                className={`h-3 rounded-full transition-all duration-1000 ${
                  timeLeft > 5 ? 'bg-green-500' : timeLeft > 3 ? 'bg-yellow-500' : 'bg-red-500'
                }`}
                style={{ width: `${(timeLeft / 15) * 100}%` }}
              ></div>
            </div>
            <span className={`ml-2 font-bold ${timeLeft > 5 ? 'text-green-600' : timeLeft > 3 ? 'text-yellow-600' : 'text-red-600'}`}>
              {timeLeft}s
            </span>
          </div>
          
          {/* Progress Bar */}
          <div className="w-full bg-gray-200 rounded-full h-2 mb-4">
            <div
              className="bg-blue-600 h-2 rounded-full transition-all duration-300"
              style={{ width: `${progress}%` }}
            ></div>
          </div>

          {/* Difficulty Badge */}
          <div className="flex justify-between items-center mb-4">
            <span className={`px-3 py-1 rounded-full text-xs font-bold ${getDifficultyColor(question.difficulty)}`}>
              {question.difficulty.toUpperCase()} QUESTION
            </span>
          </div>
        </div>

        {/* Question */}
        <div className="mb-6">
          <h2 className="text-xl font-semibold mb-6 text-gray-800">{question.question}</h2>
          
          {/* Answer Options */}
          <div className="space-y-3">
            {question.options.map((option, index) => {
              let buttonClass = "w-full text-left p-4 rounded-lg border-2 transition-all duration-200 font-medium ";
              
              if (showExplanation) {
                if (index === question.correctAnswer) {
                  buttonClass += "bg-green-100 border-green-500 text-green-800 ";
                } else if (index === selectedAnswer && index !== question.correctAnswer) {
                  buttonClass += "bg-red-100 border-red-500 text-red-800 ";
                } else {
                  buttonClass += "bg-gray-50 border-gray-300 text-gray-600 ";
                }
              } else {
                buttonClass += "bg-white border-gray-300 hover:border-blue-500 hover:bg-blue-50 text-gray-800 ";
              }

              return (
                <button
                  key={index}
                  onClick={() => handleAnswerClick(index)}
                  className={buttonClass}
                  disabled={showExplanation}
                >
                  <span className="mr-3">{index === question.correctAnswer && showExplanation ? '✓' : ''}</span>
                  {String.fromCharCode(65 + index)}. {option}
                </button>
              );
            })}
          </div>
        </div>

        {/* Explanation */}
        {showExplanation && (
          <div className="bg-blue-50 border-l-4 border-blue-500 p-4 rounded-lg mb-6">
            <p className="font-semibold text-blue-800 mb-2">
              {selectedAnswer === question.correctAnswer ? '🎉 Correct!' : '❌ Incorrect'}
            </p>
            <p className="text-blue-700">{question.explanation}</p>
          </div>
        )}

        {/* Next Button */}
        {showExplanation && (
          <button
            onClick={handleNextQuestion}
            className="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 px-6 rounded-lg text-lg transition duration-200"
          >
            {currentQuestion < questions.length - 1 ? 'Next Question →' : 'See Results'}
          </button>
        )}
      </div>
    </div>
  );
}

