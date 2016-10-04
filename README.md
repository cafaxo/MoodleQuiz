# MoodleQuiz

This module facilitates the creation of a Moodle quiz. It provides a `Quiz` data structure corresponding to the Moodle XML format for a quiz as well as functions to export this data structure to said Moodle XML Format.

[![](https://img.shields.io/badge/docs-latest-blue.svg)](https://tauu.github.io/MoodleQuiz/latest)

## Example
```julia
# a simple muliplie choice question
q1 = Question(MultipleChoice,
	     Name="Through The Galaxy",
             Text="What is the answer to the Ultimate Question of Life, The Universe, and Everything?",
	     IncorrectFeedback="Read The Hitchhiker's Guide to the Galaxy",
	     Answers=[
		Answer("21",Correct=0),
		Answer("42",Correct=1),
		Answer("0" ,Correct=0)
	     ]
     );

# a true / false question
q2 = Question(TrueFalse,
	     Name="Boldy Go Where ...",
             Text="Does god need spaceship?",
	     IncorrectFeedback="Watch number V",
	     Answers=TrueFalseAnswer()
    );

# question containing latex code (the M" " macro avoids the need to escape $,\,... )
q3  = Question(MultipleChoice,
	       Name="Circle",
	       Text=M"What is the area of a circle with radius $r$?",
	       Answers=[
	          Answer(M"$\pi$"    ,Correct=0),
	          Answer(M"$2 \pi$"  ,Correct=0),
	          Answer(M"$\pi r^2$",Correct=1)
	       ]
      );

# question containing embedded answers
a1 = EmbeddedAnswer(MultipleChoiceSelect,AnswerOptions=[EmbeddedAnswerOption("Electric",Correct=1),EmbeddedAnswerOption("Grass",Correct=0),EmbeddedAnswerOption("Water",Correct=0)]);
a2 = EmbeddedAnswer(MultipleChoiceVertical,AnswerOptions=[EmbeddedAnswerOption("not very",Correct=1),EmbeddedAnswerOption("very",Correct=0)]);
q4 = Question(EmbeddedAnswers,
			Name="Pokemon",
			Text="""
			There are $(NumericalEmbeddedAnswer(151)) 1st gen Pokemon, and $(NumericalEmbeddedAnswer(721)) Pokemon overall.<br>
			Pikachu is a $a1 type Pokemon.<br>
			Fire type moves are $a2 effective against water type Pokemon.
			"""
);

# question containing an embedded image
f1 = MoodleFile("rhp.png");
a3 = EmbeddedAnswer(MultipleChoiceVertical,AnswerOptions=[EmbeddedAnswerOption("I",Correct=0),EmbeddedAnswerOption("II",Correct=1)]);
q5 = Question(EmbeddedAnswers,
					 Name="RHP Identification",
					 Text=MoodleText("""
					 Which RHP is shown in this image?<br>
					 $(EmbedFile(f1;width="250px",height="250px"))
					 $a3
					 """,MoodleQuiz.HTML,[f1])
					 );

# question containing an embedded plot
using Gadfly # you may use any plot package which supports printing to mime type image/png
f2 = MoodleFile(plot(x=0+0:0.1:2*pi,y=sin(0:0.1:2*pi),Geom.line));
# a simple muliplie choice question
q6 = Question(MultipleChoice,
	     Name="Trigonometry",
       Text=MoodleText("""
			   Which function is depicted in the following figure?<br>
			   $(EmbedFile(f2;width="250px",height="250px"))
			   """,MoodleQuiz.HTML,[f2]),
	     Answers=[
			 	 Answer("sin",Correct=1),
				 Answer("cos",Correct=0),
				 Answer("tan",Correct=0)
	     ]
     );

# create a quiz and export it
quiz = Quiz([q1, q2, q3, q4, q5, q6]);
exportXML(quiz,"Space.xml")
```

## Currently Supported Quiz Types
* Multiple Choice
* All Or Nothing Multiple Choice
* Simple Yes/No Questions
* Embedded Answers
