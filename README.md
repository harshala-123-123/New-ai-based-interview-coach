# New-ai-based-interview-coach

"use client";

import { useState, useEffect } from "react";
import { Button } from "@/components/ui/button";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Textarea } from "@/components/ui/textarea";
import { Badge } from "@/components/ui/badge";
import { Avatar, AvatarFallback } from "@/components/ui/avatar";
import { 
  Play, 
  RotateCcw, 
  Send, 
  Star, 
  Clock, 
  User, 
  Bot 
} from "lucide-react";

// Mock data for interview questions and feedback
const MOCK_QUESTIONS = {
  developer: [
    "Tell me about a challenging bug you solved recently and how you approached it.",
    "How do you ensure your code is maintainable and scalable?",
    "Describe a time when you had to work with a team to deliver a project under a tight deadline."
  ],
  designer: [
    "How do you approach designing for accessibility?",
    "Tell me about a project where you had to balance user needs with business goals.",
    "Describe your process for conducting user research."
  ],
  manager: [
    "How do you handle conflicts within your team?",
    "Tell me about a time you had to deliver difficult feedback to a team member.",
    "Describe how you prioritize tasks when managing multiple projects."
  ]
};

const MOCK_FEEDBACK = [
  "Great answer! You demonstrated strong problem-solving skills and showed how you learn from challenges.",
  "Your response was clear and showed good technical knowledge. Consider adding more specific examples next time.",
  "You communicated your thought process well. To improve, try to be more concise while maintaining clarity.",
  "Excellent point about team collaboration. Adding metrics or specific outcomes would make this even stronger."
];

const MOCK_RATINGS = [
  { label: "Clarity", score: 4 },
  { label: "Relevance", score: 5 },
  { label: "Depth", score: 3 },
  { label: "Structure", score: 4 }
];

export default function AIInterviewCoach() {
  // State management
  const [jobRole, setJobRole] = useState("");
  const [questions, setQuestions] = useState<string[]>([]);
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [userAnswer, setUserAnswer] = useState("");
  const [feedback, setFeedback] = useState("");
  const [ratings, setRatings] = useState<{label: string, score: number}[]>([]);
  const [isInterviewActive, setIsInterviewActive] = useState(false);
  const [isEvaluating, setIsEvaluating] = useState(false);
  const [interviewHistory, setInterviewHistory] = useState<Array<{
    question: string;
    answer: string;
    feedback: string;
    ratings: {label: string, score: number}[];
  }>>([]);

  // Generate interview questions based on job role
  const generateQuestions = () => {
    if (!jobRole.trim()) return;
    
    let roleKey: keyof typeof MOCK_QUESTIONS = "developer";
    
    if (jobRole.toLowerCase().includes("design")) {
      roleKey = "designer";
    } else if (jobRole.toLowerCase().includes("manage") || jobRole.toLowerCase().includes("lead")) {
      roleKey = "manager";
    }
    
    setQuestions(MOCK_QUESTIONS[roleKey]);
    setCurrentQuestionIndex(0);
    setUserAnswer("");
    setFeedback("");
    setRatings([]);
    setIsInterviewActive(true);
    setInterviewHistory([]);
  };

  // Submit answer and get feedback
  const submitAnswer = () => {
    if (!userAnswer.trim()) return;
    
    setIsEvaluating(true);
    
    // Simulate AI processing time
    setTimeout(() => {
      const randomFeedback = MOCK_FEEDBACK[Math.floor(Math.random() * MOCK_FEEDBACK.length)];
      const randomRatings = [...MOCK_RATINGS].map(rating => ({
        ...rating,
        score: Math.floor(Math.random() * 3) + 3 // Random score between 3-5
      }));
      
      setFeedback(randomFeedback);
      setRatings(randomRatings);
      setIsEvaluating(false);
      
      // Add to interview history
      setInterviewHistory(prev => [
        ...prev,
        {
          question: questions[currentQuestionIndex],
          answer: userAnswer,
          feedback: randomFeedback,
          ratings: randomRatings
        }
      ]);
    }, 1500);
  };

  // Move to next question
  const nextQuestion = () => {
    if (currentQuestionIndex < questions.length - 1) {
      setCurrentQuestionIndex(prev => prev + 1);
      setUserAnswer("");
      setFeedback("");
      setRatings([]);
    } else {
      // End of interview
      setIsInterviewActive(false);
    }
  };

  // Reset interview
  const resetInterview = () => {
    setJobRole("");
    setQuestions([]);
    setCurrentQuestionIndex(0);
    setUserAnswer("");
    setFeedback("");
    setRatings([]);
    setIsInterviewActive(false);
    setInterviewHistory([]);
  };

  // Render star ratings
  const renderStars = (score: number) => {
    return (
      <div className="flex">
        {[...Array(5)].map((_, i) => (
          <Star 
            key={i} 
            className={`w-4 h-4 ${i < score ? "fill-yellow-400 text-yellow-400" : "text-gray-300"}`} 
          />
        ))}
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-4 md:p-8">
      <div className="max-w-4xl mx-auto">
        <header className="text-center mb-10">
          <h1 className="text-3xl md:text-4xl font-bold text-gray-800 mb-2">
            AI Interview Coach
          </h1>
          <p className="text-gray-600">
            Practice interviews and get AI-powered feedback to improve your skills
          </p>
        </header>

        <Card className="mb-8 shadow-lg">
          <CardHeader>
            <CardTitle className="flex items-center gap-2">
              <Bot className="w-5 h-5 text-blue-500" />
              Interview Setup
            </CardTitle>
          </CardHeader>
          <CardContent>
            {!isInterviewActive ? (
              <div className="space-y-6">
                <div className="space-y-2">
                  <Label htmlFor="job-role">Job Role</Label>
                  <Input
                    id="job-role"
                    placeholder="e.g., Frontend Developer, Product Manager, UX Designer"
                    value={jobRole}
                    onChange={(e) => setJobRole(e.target.value)}
                    className="text-lg"
                  />
                </div>
                
                <Button 
                  onClick={generateQuestions} 
                  className="w-full py-6 text-lg"
                  disabled={!jobRole.trim()}
                >
                  <Play className="w-5 h-5 mr-2" />
                  Start Interview
                </Button>
              </div>
            ) : (
              <div className="flex flex-col sm:flex-row justify-between items-center gap-4">
                <div>
                  <h3 className="font-semibold text-lg">Interview in Progress</h3>
                  <p className="text-gray-600">
                    Role: <span className="font-medium">{jobRole}</span>
                  </p>
                </div>
                <Button 
                  onClick={resetInterview} 
                  variant="outline"
                  className="flex items-center gap-2"
                >
                  <RotateCcw className="w-4 h-4" />
                  Reset Interview
                </Button>
              </div>
            )}
          </CardContent>
        </Card>

        {isInterviewActive && questions.length > 0 && (
          <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
            {/* Question Panel */}
            <Card className="shadow-lg">
              <CardHeader className="bg-blue-50 rounded-t-lg">
                <div className="flex items-center justify-between">
                  <CardTitle className="flex items-center gap-2">
                    <User className="w-5 h-5 text-blue-500" />
                    Question {currentQuestionIndex + 1} of {questions.length}
                  </CardTitle>
                  <Badge variant="secondary">
                    {Math.round(((currentQuestionIndex + 1) / questions.length) * 100)}%
                  </Badge>
                </div>
              </CardHeader>
              <CardContent className="pt-6">
                <div className="mb-6 p-4 bg-white rounded-lg border border-gray-200">
                  <p className="text-lg font-medium text-gray-800">
                    {questions[currentQuestionIndex]}
                  </p>
                </div>
                
                <div className="space-y-4">
                  <Label htmlFor="answer">Your Answer</Label>
                  <Textarea
                    id="answer"
                    placeholder="Type your answer here..."
                    value={userAnswer}
                    onChange={(e) => setUserAnswer(e.target.value)}
                    rows={6}
                    className="text-base"
                  />
                  
                  <div className="flex flex-wrap gap-3">
                    <Button 
                      onClick={submitAnswer} 
                      disabled={!userAnswer.trim() || isEvaluating}
                      className="flex-1 min-w-[150px]"
                    >
                      {isEvaluating ? (
                        <>
                          <Clock className="w-4 h-4 mr-2 animate-spin" />
                          Evaluating...
                        </>
                      ) : (
                        <>
                          <Send className="w-4 h-4 mr-2" />
                          Submit Answer
                        </>
                      )}
                    </Button>
                    
                    {currentQuestionIndex < questions.length - 1 && feedback && (
                      <Button 
                        onClick={nextQuestion} 
                        variant="outline"
                        className="flex-1 min-w-[150px]"
                      >
                        Next Question
                      </Button>
                    )}
                  </div>
                </div>
              </CardContent>
            </Card>

            {/* Feedback Panel */}
            <Card className="shadow-lg">
              <CardHeader className="bg-green-50 rounded-t-lg">
                <CardTitle className="flex items-center gap-2">
                  <Bot className="w-5 h-5 text-green-500" />
                  AI Feedback
                </CardTitle>
              </CardHeader>
              <CardContent className="pt-6">
                {feedback ? (
                  <div className="space-y-6">
                    <div>
                      <h3 className="font-semibold text-gray-700 mb-2">Overall Feedback</h3>
                      <div className="p-4 bg-white rounded-lg border border-gray-200">
                        <p className="text-gray-800">{feedback}</p>
                      </div>
                    </div>
                    
                    <div>
                      <h3 className="font-semibold text-gray-700 mb-2">Evaluation Metrics</h3>
                      <div className="space-y-3">
                        {ratings.map((rating, index) => (
                          <div key={index} className="flex items-center justify-between p-3 bg-white rounded-lg border border-gray-200">
                            <span className="font-medium text-gray-700">{rating.label}</span>
                            <div className="flex items-center gap-2">
                              <span className="text-sm font-medium w-6">{rating.score}/5</span>
                              {renderStars(rating.score)}
                            </div>
                          </div>
                        ))}
                      </div>
                    </div>
                    
                    <div className="pt-4 border-t border-gray-200">
                      <h3 className="font-semibold text-gray-700 mb-2">Suggestions for Improvement</h3>
                      <ul className="list-disc pl-5 space-y-1 text-gray-700">
                        <li>Include specific examples from your past experience</li>
                        <li>Structure your response using the STAR method (Situation, Task, Action, Result)</li>
                        <li>Focus on quantifiable achievements when possible</li>
                      </ul>
                    </div>
                  </div>
                ) : (
                  <div className="flex flex-col items-center justify-center h-full py-12 text-center">
                    <Bot className="w-12 h-12 text-gray-400 mb-4" />
                    <h3 className="text-lg font-medium text-gray-700 mb-2">Submit Your Answer</h3>
                    <p className="text-gray-500">
                      After submitting your answer, our AI will provide personalized feedback to help you improve.
                    </p>
                  </div>
                )}
              </CardContent>
            </Card>
          </div>
        )}

        {interviewHistory.length > 0 && (
          <Card className="mt-8 shadow-lg">
            <CardHeader>
              <CardTitle>Interview History</CardTitle>
            </CardHeader>
            <CardContent>
              <div className="space-y-6">
                {interviewHistory.map((item, index) => (
                  <div key={index} className="border border-gray-200 rounded-lg p-4">
                    <div className="flex items-start gap-3 mb-3">
                      <Avatar className="w-8 h-8">
                        <AvatarFallback>Q{index + 1}</AvatarFallback>
                      </Avatar>
                      <div>
                        <h4 className="font-medium text-gray-800">Question {index + 1}</h4>
                        <p className="text-gray-600">{item.question}</p>
                      </div>
                    </div>
                    
                    <div className="ml-11 space-y-3">
                      <div>
                        <h5 className="font-medium text-gray-700 mb-1">Your Answer</h5>
                        <p className="text-gray-600 bg-gray-50 p-3 rounded-md">{item.answer}</p>
                      </div>
                      
                      <div>
                        <h5 className="font-medium text-gray-700 mb-1">AI Feedback</h5>
                        <p className="text-gray-600 bg-green-50 p-3 rounded-md">{item.feedback}</p>
                      </div>
                      
                      <div>
                        <h5 className="font-medium text-gray-700 mb-2">Ratings</h5>
                        <div className="flex flex-wrap gap-4">
                          {item.ratings.map((rating, ratingIndex) => (
                            <div key={ratingIndex} className="flex items-center gap-2">
                              <span className="text-sm text-gray-600">{rating.label}:</span>
                              <div className="flex items-center gap-1">
                                {renderStars(rating.score)}
                                <span className="text-sm font-medium">{rating.score}/5</span>
                              </div>
                            </div>
                          ))}
                        </div>
                      </div>
                    </div>
                  </div>
                ))}
              </div>
            </CardContent>
          </Card>
        )}

        {!isInterviewActive && !jobRole && (
          <Card className="shadow-lg">
            <CardContent className="pt-6">
              <div className="text-center py-8">
                <div className="mx-auto w-24 h-24 bg-blue-100 rounded-full flex items-center justify-center mb-6">
                  <Bot className="w-12 h-12 text-blue-500" />
                </div>
                <h3 className="text-xl font-semibold text-gray-800 mb-2">
                  Ready to Practice Your Interview Skills?
                </h3>
                <p className="text-gray-600 max-w-md mx-auto mb-6">
                  Enter your target job role to get started with AI-powered interview questions and personalized feedback.
                </p>
                <Button 
                  onClick={() => document.getElementById("job-role")?.focus()}
                  className="px-8"
                >
                  Get Started
                </Button>
              </div>
            </CardContent>
          </Card>
        )}
      </div>
    </div>
  );
}
