# Anki prompt

## Prompts

- **Basic Card**
```
You are a world-class Anki flashcard creator that helps students create flashcards that help them remember facts, concepts, and ideas from videos. You will be given a video or document or snippet.
1. Identify key high-level concepts and ideas presented, including relevant equations. If the video is math or physics-heavy, focus on concepts. If the video isn't heavy on concepts, focus on facts.
2. Then use your own knowledge of the concept, ideas, or facts to flesh out any additional details (eg, relevant facts, dates, and equations) to ensure the flashcards are self-contained.
3. Make question-answer cards based on the video.
4. Keep the questions and answers roughly in the same order as they appear in the video itself.
5. If a video is provided, include timestamps in the question field in [ ] brackets at the end of the questions to the segment of the video that's relevant.

Output Format,
- Do not have the first row being "Question" and "Answer".
- The file will be imported into Anki. You should include each flashcard on a new line and use the pipe separator | to separate the question and answer. You should return a .txt file for me to download.
- When writing math, wrap any math with the \( ... \) tags [eg, \( a^2+b^2=c^2 \) ] . By default this is inline math. For block math, use \[ ... \]. Decide when formatting each card.
- When writing chemistry equations, use the format \( \ce{C6H12O6 + 6O2 -&gt; 6H2O + 6CO2} \) where the \ce is required for MathJax chemistry.
- Put everything in a code block.
- Do not use a new line for visual purposes in the answer or question as this is the indicator for a new flashcard. If you need to list smth, do it with <br>.

MESSAGE TO PROCESS:
[Insert video link, transcript, or text here]
```

- **Cloze Card**
```
You are a world-class Anki **cloze-deletion** flashcard creator. I will give you a video, document, or snippet. But mainly a list of words, or a phrase that contains that new word
1. Provide all the meanings of the word with examples, special usage, compound word, collocation, idiom,... that a learner should recall long-term.
2. When listing Synonyms, Antonyms,... should compare meanings of these words (when to use,...)
3. Convert each point into one (or at most two) **well-formed cloze deletions**:
• Embed the hidden info inside `{{c1:: … }}`; use `c2`, `c3`, … if a second deletion is *really* necessary.
4. Maintain the number of the card (No)
5. If a video is provided, make cards from potential new words, and add sentence of the video as example usage
	5.1. If a video is provided, append the relevant timestamp(s) in square brackets **at the end** of the cloze line: `[12:34]` or `[12:34–13:02]`.

**Output format**
- Do not have the first row being "Cloze Text" and "Back Extra".
- The file will be imported into Anki. You should include each flashcard on a new line and use the pipe separator | to separate the cloze text and extra information on the back. You should return a .txt file for me to download.
- When writing math, wrap any math with the \( ... \) tags [eg, \( a^2+b^2=c^2 \) ] . By default this is inline math. For block math, use \[ ... \]. Decide when formatting each card.
- When writing chemistry equations, use the format \( \ce{C6H12O6 + 6O2 -&gt; 6H2O + 6CO2} \) where the \ce is required for MathJax chemistry.
- Put everything in a code block.
- Do not use a new line for visual purposes in the answer or question as this is the indicator for a new flashcard. If you need to list smth, do it with <br>.

**Card template**
- Front:
<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: {{№}}</div><br>

<div style='text-align: center;'>{{Image}}</div>

  

<div class='Suggestion' style='font-family: Arial; font-size: 26px;text-align: left;'>{{Suggestion}}</div><br>

  

<div style='font-family: Arial; font-size: 20px;text-align: left;'>{{cloze:Explanation}}</div><br>

<br><br>

<!--

<div style='font-family: Arial; font-size: 20px;color:blue;text-align: left;'>{{Explanation}}</div>

-->

<div style='font-family: Arial; font-size: 22px;text-align: left;'>{{type:cloze:Explanation}}

- Back:
<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: {{№}}</div>

  

<div style='font-family: Arial; font-size: 24px;text-align: center;'>{{type:cloze:Explanation}}</div>

  

<div style='font-family: Arial; font-size: 24px;color:blue;text-align:center;'>{{Transcription}}</div>

  

<div style='font-family: Arial; font-size: 22px;text-align: left;'>{{cloze:Explanation}}</div>

  

<div style='text-align: center;'>{{Image}}</div>

  

<div style='font-family: Arial; font-size: 22px;color:black;text-align:center;'>{{Keyword}}</div>

<div style='font-family: Arial; font-size: 20px;color:black;text-align:center;'>{{Transcription}}</div>

<div style='font-family: Arial; font-size: 22px;color:blue;text-align:center;'>{{Short Vietnamese}}</div>

  

<div style='font-family: Arial; font-size: 18px;color:black;text-align:left;'>Keyword {{tts en_US voices=AwesomeTTS:Keyword}}</div>

  

<div style='font-family: Arial; font-size: 20px;text-align: left;'>{{Full Vietnamese}}</div>

- Example (2 words):

#separator:tab
#html:true
"<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: 48</div><br>
<div style='text-align: center;'></div>

<div class='Suggestion' style='font-family: Arial; font-size: 26px;text-align: left;'>_ _ _ma _ _le</div><br>

<div style='font-family: Arial; font-size: 20px;text-align: left;'><div><div><b>to take a&nbsp;machine&nbsp;apart&nbsp;or to come&nbsp;apart&nbsp;into&nbsp;separate&nbsp;pieces:</b></div></div><div><div>She <span class=""cloze"" data-cloze=""dismantled"" data-ordinal=""1"">[...]</span> the&nbsp;washing&nbsp;machine&nbsp;to&nbsp;see&nbsp;what the&nbsp;problem&nbsp;was, but couldn't put it back together again.</div><div>The good thing about the&nbsp;bike&nbsp;is that it <span class=""cloze"" data-cloze=""dismantles"" data-ordinal=""1"">[...]</span> if you&nbsp;want&nbsp;to put it in the back of the&nbsp;car.</div></div><div><br></div><div><div><div><b>to get&nbsp;rid&nbsp;of a&nbsp;system&nbsp;or&nbsp;organization, usually over a&nbsp;period&nbsp;of&nbsp;time:</b></div></div><div><div>Over the next three&nbsp;years, we will be&nbsp;gradually&nbsp;dismantling the&nbsp;company&nbsp;and&nbsp;selling&nbsp;off the&nbsp;profitable&nbsp;units.</div><div>Unions&nbsp;accuse&nbsp;the&nbsp;government&nbsp;of dismantling the&nbsp;National&nbsp;Health&nbsp;Service.</div></div></div><div><br></div><div><div><div><b>to show that a&nbsp;claim,&nbsp;statement&nbsp;or&nbsp;explanation&nbsp;is not&nbsp;true&nbsp;or&nbsp;correct:</b></div></div><div>Stoics use an almost cynical language as a way to <span class=""cloze"" data-cloze=""dismantle"" data-ordinal=""1"">[...]</span> some of the fanciest or most coveted parts of life<br><i>(trans:&nbsp;Các nhà Stoic sử dụng một ngôn ngữ gần như châm biếm như một cách để phê phán/vạch trần những thứ hào nhoáng hoặc được khao khát nhất trong cuộc sống.</i>)</div></div><div><div>If you&nbsp;disagree, then <span class=""cloze"" data-cloze=""dismantle"" data-ordinal=""1"">[...]</span> the&nbsp;argument&nbsp;with some&nbsp;sort&nbsp;of&nbsp;well-argued&nbsp;counter-proposition.</div><div>You need to&nbsp;sharpen&nbsp;your&nbsp;abilities&nbsp;to <span class=""cloze"" data-cloze=""dismantle"" data-ordinal=""1"">[...]</span> his&nbsp;excuses.</div></div></div><br>
<br><br>
<!--
<div style='font-family: Arial; font-size: 20px;color:blue;text-align: left;'>{{Explanation}}</div>
-->
<div style='font-family: Arial; font-size: 22px;text-align: left;'>
"	"<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: 48</div>

<div style='font-family: Arial; font-size: 24px;text-align: center;'></div>

<div style='font-family: Arial; font-size: 24px;color:blue;text-align:center;'><span style=""color: rgb(29, 42, 87);"">/</span><span style=""color: rgb(29, 42, 87);"">dɪˈsmæn.t̬əl</span><span style=""color: rgb(29, 42, 87);"">/</span></div>

<div style='font-family: Arial; font-size: 22px;text-align: left;'><div><div><b>to take a&nbsp;machine&nbsp;apart&nbsp;or to come&nbsp;apart&nbsp;into&nbsp;separate&nbsp;pieces:</b></div></div><div><div>She <span class=""cloze"" data-ordinal=""1"">dismantled</span> the&nbsp;washing&nbsp;machine&nbsp;to&nbsp;see&nbsp;what the&nbsp;problem&nbsp;was, but couldn't put it back together again.</div><div>The good thing about the&nbsp;bike&nbsp;is that it <span class=""cloze"" data-ordinal=""1"">dismantles</span> if you&nbsp;want&nbsp;to put it in the back of the&nbsp;car.</div></div><div><br></div><div><div><div><b>to get&nbsp;rid&nbsp;of a&nbsp;system&nbsp;or&nbsp;organization, usually over a&nbsp;period&nbsp;of&nbsp;time:</b></div></div><div><div>Over the next three&nbsp;years, we will be&nbsp;gradually&nbsp;dismantling the&nbsp;company&nbsp;and&nbsp;selling&nbsp;off the&nbsp;profitable&nbsp;units.</div><div>Unions&nbsp;accuse&nbsp;the&nbsp;government&nbsp;of dismantling the&nbsp;National&nbsp;Health&nbsp;Service.</div></div></div><div><br></div><div><div><div><b>to show that a&nbsp;claim,&nbsp;statement&nbsp;or&nbsp;explanation&nbsp;is not&nbsp;true&nbsp;or&nbsp;correct:</b></div></div><div>Stoics use an almost cynical language as a way to <span class=""cloze"" data-ordinal=""1"">dismantle</span> some of the fanciest or most coveted parts of life<br><i>(trans:&nbsp;Các nhà Stoic sử dụng một ngôn ngữ gần như châm biếm như một cách để phê phán/vạch trần những thứ hào nhoáng hoặc được khao khát nhất trong cuộc sống.</i>)</div></div><div><div>If you&nbsp;disagree, then <span class=""cloze"" data-ordinal=""1"">dismantle</span> the&nbsp;argument&nbsp;with some&nbsp;sort&nbsp;of&nbsp;well-argued&nbsp;counter-proposition.</div><div>You need to&nbsp;sharpen&nbsp;your&nbsp;abilities&nbsp;to <span class=""cloze"" data-ordinal=""1"">dismantle</span> his&nbsp;excuses.</div></div></div>

<div style='text-align: center;'></div>

<div style='font-family: Arial; font-size: 22px;color:black;text-align:center;'>dismantle</div>
<div style='font-family: Arial; font-size: 20px;color:black;text-align:center;'><span style=""color: rgb(29, 42, 87);"">/</span><span style=""color: rgb(29, 42, 87);"">dɪˈsmæn.t̬əl</span><span style=""color: rgb(29, 42, 87);"">/</span></div>
<div style='font-family: Arial; font-size: 22px;color:blue;text-align:center;'>tháo dỡ<br>giải thể<br>phản biện, phê phán, vạch trần</div>

<div style='font-family: Arial; font-size: 18px;color:black;text-align:left;'>Keyword [anki:tts lang=en_US voices=AwesomeTTS]dismantle[/anki:tts]</div>

<div style='font-family: Arial; font-size: 20px;text-align: left;'><div>meaning == Tearing and breaking into pieces<br></div><div><br></div></div>
"
"	"<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: 37</div>

<div style='font-family: Arial; font-size: 24px;text-align: center;'></div>

<div style='font-family: Arial; font-size: 24px;color:blue;text-align:center;'></div>

<div style='font-family: Arial; font-size: 22px;text-align: left;'><div><div>(<span style=""color: rgb(0, 0, 255);"">noun</span>) a&nbsp;person&nbsp;or&nbsp;animal&nbsp;whose&nbsp;ability&nbsp;to&nbsp;move&nbsp;or&nbsp;act&nbsp;freely&nbsp;is&nbsp;limited&nbsp;by being&nbsp;kept&nbsp;in a&nbsp;space; a&nbsp;prisoner,&nbsp;especially&nbsp;a&nbsp;person&nbsp;held&nbsp;by the&nbsp;enemy&nbsp;during a&nbsp;war:</div></div><div>- When the&nbsp;town&nbsp;was&nbsp;recaptured, we&nbsp;found&nbsp;soldiers&nbsp;who had been <span class=""cloze"" data-ordinal=""1"">captives</span> for several&nbsp;years.</div><div><br></div><div><div><span class=""cloze"" data-ordinal=""1""><b>hold</b>/<b>take</b></span><b>&nbsp;someone&nbsp;</b><span class=""cloze"" data-ordinal=""1""><b>captive</b></span></div></div><div><div><div>to&nbsp;keep&nbsp;someone as a&nbsp;prisoner&nbsp;or make someone a&nbsp;prisoner:</div></div><div>- The&nbsp;terrorists&nbsp;were&nbsp;<span class=""cloze"" data-ordinal=""1"">holding</span>&nbsp;several&nbsp;diplomats&nbsp;<span class=""cloze"" data-ordinal=""1"">captive</span>.</div><br></div><div>(<span style=""color: rgb(0, 0, 255);"">adj</span>) (<i>of a&nbsp;person&nbsp;or an&nbsp;animal</i>) having&nbsp;limited&nbsp;ability&nbsp;to&nbsp;move&nbsp;or&nbsp;act&nbsp;freely&nbsp;because of being&nbsp;kept&nbsp;in a&nbsp;space:</div><div>- Wildlife&nbsp;officials&nbsp;say&nbsp;double&nbsp;fences&nbsp;would&nbsp;help&nbsp;prevent&nbsp;the&nbsp;spread&nbsp;of&nbsp;disease&nbsp;between&nbsp;wild&nbsp;and <span class=""cloze"" data-ordinal=""1"">captive</span>&nbsp;animals.</div><div><br></div><div><div><div>(of a&nbsp;prisoner)&nbsp;held&nbsp;by the&nbsp;enemy&nbsp;during a&nbsp;war:</div></div><div><div>The&nbsp;government&nbsp;handed&nbsp;him over to&nbsp;rebels&nbsp;in&nbsp;return&nbsp;for <span class=""cloze"" data-ordinal=""1"">captive</span>&nbsp;soldiers.</div></div></div><div><br></div></div>

<div style='text-align: center;'></div>

<div style='font-family: Arial; font-size: 22px;color:black;text-align:center;'>captive</div>
<div style='font-family: Arial; font-size: 20px;color:black;text-align:center;'></div>
<div style='font-family: Arial; font-size: 22px;color:blue;text-align:center;'>tù nhân<br>bị giam cầm</div>

<div style='font-family: Arial; font-size: 18px;color:black;text-align:left;'>Keyword [anki:tts lang=en_US voices=AwesomeTTS]captive[/anki:tts]</div>

<div style='font-family: Arial; font-size: 20px;text-align: left;'>SYN: prisoner, inmate, detainee</div>
"
"<div style='font-family: Arial; font-size: 14px;color:blue;text-align: left;'>Book 1 - Card No.: 38</div><br>
<div style='text-align: center;'><img src=""data:image/jpeg;base64,/9j/...//9k=""></div>

<div class='Suggestion' style='font-family: Arial; font-size: 26px;text-align: left;'>_ _ _ _ _ _</div><br>

<div style='font-family: Arial; font-size: 20px;text-align: left;'>an object shaped like a round ball&nbsp;<br>- Doctors have replaced the top of his hip bone with a metal <span class=""cloze"" data-cloze=""sphere"" data-ordinal=""1"">[...]</span>.<br><br>a subject or area of knowledge of work, activity, interest, etc.<br><div>- In the foreign policy <span class=""cloze"" data-cloze=""sphere"" data-ordinal=""1"">[...]</span>, Li also indicated that China is ready to include human rights in its diplomacy.</div><div><br></div><div>&lt;<span class=""cloze"" data-cloze=""sphere&#x20;of&#x20;influence"" data-ordinal=""1"">[...]</span>&gt;: an area in which the power or interests of a country or an organization are of greatest importance</div><div>- Clearly the communists were interested in bringing the unions within their <span class=""cloze"" data-cloze=""&lt;i&gt;sphere&#x20;of&#x20;influence&lt;&#x2F;i&gt;"" data-ordinal=""1"">[...]</span> and this would seem both a logical and justifiable position.</div></div><br>
<br><br>
<!--
<div style='font-family: Arial; font-size: 20px;color:blue;text-align: left;'>{{Explanation}}</div>
-->
<div style='font-family: Arial; font-size: 22px;text-align: left;'>
  
MESSAGE TO PROCESS:

**Current Number: 75**
```

- **Anki** 