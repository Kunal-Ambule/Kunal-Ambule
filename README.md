import { useState } from 'react';
import { Save, Plus, RotateCcw, Check, X } from 'lucide-react';

export default function FlashcardQuizApp() {
  const [cards, setCards] = useState([]);
  const [currentCard, setCurrentCard] = useState(0);
  const [showAnswer, setShowAnswer] = useState(false);
  const [mode, setMode] = useState('create'); // 'create', 'quiz', 'results'
  const [newQuestion, setNewQuestion] = useState('');
  const [newAnswer, setNewAnswer] = useState('');
  const [results, setResults] = useState({ correct: 0, incorrect: 0 });
  const [userAnswers, setUserAnswers] = useState([]);

  const addCard = () => {
    if (newQuestion.trim() === '' || newAnswer.trim() === '') return;
    
    setCards([...cards, { question: newQuestion, answer: newAnswer }]);
    setNewQuestion('');
    setNewAnswer('');
  };

  const startQuiz = () => {
    if (cards.length === 0) return;
    
    setMode('quiz');
    setCurrentCard(0);
    setShowAnswer(false);
    setResults({ correct: 0, incorrect: 0 });
    setUserAnswers(Array(cards.length).fill(null));
  };

  const handleNextCard = () => {
    if (currentCard < cards.length - 1) {
      setCurrentCard(currentCard + 1);
      setShowAnswer(false);
    } else {
      setMode('results');
    }
  };

  const markAnswer = (isCorrect) => {
    const newUserAnswers = [...userAnswers];
    newUserAnswers[currentCard] = isCorrect;
    setUserAnswers(newUserAnswers);
    
    setResults({
      correct: isCorrect ? results.correct + 1 : results.correct,
      incorrect: !isCorrect ? results.incorrect + 1 : results.incorrect
    });
    
    handleNextCard();
  };

  const resetApp = () => {
    setMode('create');
    setCurrentCard(0);
    setShowAnswer(false);
  };

  return (
    <div className="flex flex-col items-center w-full max-w-md mx-auto p-4 bg-gray-50 rounded-lg shadow-md">
      <h1 className="text-2xl font-bold text-blue-600 mb-4">Flashcard Quiz App</h1>
      
      {mode === 'create' && (
        <>
          <div className="w-full mb-4 p-4 bg-white rounded-lg shadow">
            <h2 className="text-lg font-semibold mb-2">Create Flashcards</h2>
            <div className="space-y-3">
              <div>
                <label className="block text-sm font-medium mb-1">Question:</label>
                <textarea 
                  className="w-full p-2 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={newQuestion}
                  onChange={(e) => setNewQuestion(e.target.value)}
                  rows={2}
                />
              </div>
              <div>
                <label className="block text-sm font-medium mb-1">Answer:</label>
                <textarea 
                  className="w-full p-2 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500"
                  value={newAnswer}
                  onChange={(e) => setNewAnswer(e.target.value)}
                  rows={2}
                />
              </div>
              <button 
                className="flex items-center justify-center px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                onClick={addCard}
              >
                <Plus size={16} className="mr-1" /> Add Card
              </button>
            </div>
          </div>
          
          <div className="w-full mb-4">
            <h2 className="text-lg font-semibold mb-2">Your Flashcards ({cards.length})</h2>
            {cards.length === 0 ? (
              <p className="text-gray-500 italic">No flashcards yet. Add some above!</p>
            ) : (
              <div className="space-y-2 max-h-64 overflow-y-auto">
                {cards.map((card, index) => (
                  <div key={index} className="p-3 bg-white rounded shadow">
                    <p className="font-medium">{card.question}</p>
                    <p className="text-gray-600 text-sm mt-1">Answer: {card.answer}</p>
                  </div>
                ))}
              </div>
            )}
          </div>
          
          <button 
            className={`w-full py-2 rounded font-medium ${cards.length > 0 ? 'bg-blue-500 text-white hover:bg-blue-600' : 'bg-gray-300 text-gray-500 cursor-not-allowed'}`}
            onClick={startQuiz}
            disabled={cards.length === 0}
          >
            Start Quiz
          </button>
        </>
      )}
      
      {mode === 'quiz' && (
        <div className="w-full">
          <div className="flex justify-between items-center mb-4">
            <span className="text-sm font-medium">Card {currentCard + 1} of {cards.length}</span>
            <button className="text-blue-500 hover:text-blue-600" onClick={resetApp}>
              <RotateCcw size={16} />
            </button>
          </div>
          
          <div className="w-full mb-4 p-6 bg-white rounded-lg shadow-md flex flex-col items-center min-h-64 justify-center">
            <h2 className="text-xl font-semibold mb-4">{cards[currentCard].question}</h2>
            
            {showAnswer ? (
              <>
                <p className="text-lg mb-4">{cards[currentCard].answer}</p>
                <div className="flex space-x-4 mt-4">
                  <button 
                    className="flex items-center px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600"
                    onClick={() => markAnswer(true)}
                  >
                    <Check size={16} className="mr-1" /> Correct
                  </button>
                  <button 
                    className="flex items-center px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600"
                    onClick={() => markAnswer(false)}
                  >
                    <X size={16} className="mr-1" /> Incorrect
                  </button>
                </div>
              </>
            ) : (
              <button 
                className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 mt-4"
                onClick={() => setShowAnswer(true)}
              >
                Show Answer
              </button>
            )}
          </div>
          
          <div className="w-full bg-gray-200 h-2 rounded-full overflow-hidden">
            <div 
              className="bg-blue-500 h-full" 
              style={{ width: `${((currentCard + 1) / cards.length) * 100}%` }}
            ></div>
          </div>
        </div>
      )}
      
      {mode === 'results' && (
        <div className="w-full">
          <h2 className="text-xl font-semibold mb-4">Quiz Results</h2>
          
          <div className="w-full mb-6 p-6 bg-white rounded-lg shadow text-center">
            <div className="text-4xl font-bold text-blue-600 mb-2">
              {Math.round((results.correct / cards.length) * 100)}%
            </div>
            <p className="text-lg">
              You got <span className="font-medium text-green-600">{results.correct}</span> out of <span className="font-medium">{cards.length}</span> correct
            </p>
          </div>
          
          <div className="space-y-3 mb-6">
            <h3 className="font-medium">Card Review:</h3>
            {cards.map((card, index) => (
              <div key={index} className={`p-3 rounded shadow ${userAnswers[index] === true ? 'bg-green-50 border-l-4 border-green-500' : userAnswers[index] === false ? 'bg-red-50 border-l-4 border-red-500' : 'bg-white'}`}>
                <p className="font-medium">{card.question}</p>
                <p className="text-gray-600 text-sm mt-1">Answer: {card.answer}</p>
                {userAnswers[index] !== null && (
                  <p className={`text-xs mt-1 ${userAnswers[index] ? 'text-green-600' : 'text-red-600'}`}>
                    {userAnswers[index] ? 'You got this correct!' : 'You missed this one'}
                  </p>
                )}
              </div>
            ))}
          </div>
          
          <div className="flex space-x-4">
            <button 
              className="flex-1 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 font-medium"
              onClick={startQuiz}
            >
              Retry Quiz
            </button>
            <button 
              className="flex-1 py-2 bg-gray-200 text-gray-800 rounded hover:bg-gray-300 font-medium"
              onClick={resetApp}
            >
              Back to Cards
            </button>
          </div>
        </div>
      )}
    </div>
  );
}
<!---
Kunal-Ambule/Kunal-Ambule is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
