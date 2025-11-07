You are a world-class Anki flashcard creator. I will give you a video, document, or snippet. But mainly a list of words, some word may have a sentence that contains that new word
1. Provide all the meanings of the word with examples, special usage, compound word, collocation, idiom,... that a learner should recall long-term.
2. When listing Synonyms, Antonyms,... should compare meanings of these words (when to use,...)
3. Convert each point into one (or at most two) **well-formed cloze deletions**:
	• Embed the hidden info inside `{{c1:: … }}`; use `c2`, `c3`, … if a second deletion is *really* necessary.
4. Maintain the number of the card (No)
5. If a video is provided, make cards from potential new words, and add sentence of the video as example usage
	5.1. If a video is provided, append the relevant timestamp(s) in square brackets **at the end** of the cloze line: `[12:34]` or `[12:34–13:02]`.

**Output format**
- File format: .csv ; Separator: pipe (|)
- Put everything in a code block.
- Follow format from the **Example Data** belows. I also provide **CSS Style** of the template.
- The template have these fields:
	- No: number for identifier (unique, increasing numbers)
	- Keyword
	- Suggestion: just show some character of the word, separate each word by space. For ex, steadiness --> _ _ _adi_ _ _ _
	- Short Vietnamese: list of vietnamese meanings
	- Image: optional
	- Transcription: 
	- Explanation:
	- Notes: 
	- Tags:
- The file will be imported into Anki. You should return a .csv file for me to download.
- Field **Notes** contains: Synonyms, Antonyms, Special Usages... 

**Example Data**
```
"101"|"steadiness"|"_ _ _adi_ _ _ _"|"sự kiên định<br>sự đáng tin cậy, sự kiên quyết<br><strong>Sự vững vàng</strong> hoặc <strong>sự đều đặn<br></strong>"|""|"/ˈsted.i.nəs/"|"<div><div><ul><li><span style=""font-style: italic;"">Strength, {{c1::steadiness}}, and sang-froid are</span><span style=""font-style: italic;"">&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/essential"" style=""font-style: italic;"">essential</a><span style=""font-style: italic;"">&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/trait"" style=""font-style: italic;"">traits</a><span style=""font-style: italic;"">&nbsp;</span><span style=""font-style: italic;"">for a</span><span style=""font-style: italic;"">&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/president"" style=""font-style: italic;"">president</a><span style=""font-style: italic;"">.</span></li><li><span style=""font-style: italic;"">He&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/display"">displayed</a>&nbsp;{{c1::steadiness}},&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/intellectual"">intellectual</a>&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/curiosity"">curiosity</a>, and a&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/depth"">depth</a>&nbsp;of&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/knowledge"">knowledge</a>.</span></li></ul></div></div><div><div><div><b><a href=""https://dictionary.cambridge.org/vi/dictionary/english/behaviour"">behaviour</a>&nbsp;that is&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/reasonable"">reasonable</a>&nbsp;and&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/show"">shows</a>&nbsp;good&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/judgment"">judgment</a>, so that&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/people"">people</a>&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/trust"">trust</a>&nbsp;you:</b></div></div><div><br></div><div><div><div><ul><li><span style=""font-style: italic;"">She&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/held"" style=""font-style: italic;"">held</a><span style=""font-style: italic;"">&nbsp;the high&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/note"" style=""font-style: italic;"">notes</a><span style=""font-style: italic;"">&nbsp;with&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/amazing"" style=""font-style: italic;"">amazing</a><span style=""font-style: italic;"">&nbsp;{{c1::steadiness}}.</span></li><li><span style=""font-style: italic;"">The {{c1::steadiness}}&nbsp;of his&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/eye"">eyes</a>&nbsp;on her did not&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/perturb"">perturb</a>&nbsp;her.</span></li></ul></div></div></div><div><div><div><b>the&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/quality"">quality</a>&nbsp;of being under&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/control"">control</a>:</b></div></div><div></div></div><div><br></div><div><ul><li><span style=""font-style: italic;"">The&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/timing"" style=""font-style: italic;"">timing</a><span style=""font-style: italic;"">&nbsp;of the&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/change"" style=""font-style: italic;"">change</a><span style=""font-style: italic;"">&nbsp;came at a&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/period"" style=""font-style: italic;"">period</a><span style=""font-style: italic;"">&nbsp;of&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/relative"" style=""font-style: italic;"">relative</a><span style=""font-style: italic;"">&nbsp;{{c1::steadiness}}&nbsp;in&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/interest"" style=""font-style: italic;"">interest</a><span style=""font-style: italic;"">&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/rate"" style=""font-style: italic;"">rates</a><span style=""font-style: italic;"">.</span></li><li><span style=""font-style: italic;"">She&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/find"" style=""font-style: italic;"">finds</a><span style=""font-style: italic;"">&nbsp;the {{c1::steadiness}}&nbsp;of&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/life"" style=""font-style: italic;"">life</a><span style=""font-style: italic;"">&nbsp;with an&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/old"" style=""font-style: italic;"">older</a><span style=""font-style: italic;"">&nbsp;man&nbsp;</span><a href=""https://dictionary.cambridge.org/vi/dictionary/english/appealing"" style=""font-style: italic;"">appealing</a><span style=""font-style: italic;"">.</span></li></ul></div><div><b>the&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/quality"">quality</a>&nbsp;of not&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/changing"">changing</a>&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/suddenly"">suddenly</a>:</b><br></div><div><div></div></div><div><br></div><div><ul><li><span style=""font-style: italic;"">She could&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/hear"">hear</a>&nbsp;the {{c1::steadiness}}&nbsp;of his&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/heartbeat"">heartbeat</a>.</span></li></ul></div><div><b>The&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/fact"">fact</a>&nbsp;of being&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/gradual"">gradual</a>&nbsp;or&nbsp;<a href=""https://dictionary.cambridge.org/vi/dictionary/english/regular"">regular</a></b></div><div><br></div><div><ul>
<li>
<div><strong>Example</strong>: <em>""The {{c1::steadiness}}&nbsp;of the ship in rough seas was remarkable.""</em><br>
<strong>""Sự vững vàng của con tàu trong biển động thật ấn tượng.""</strong></div>
</li>
</ul><div><h3>The ability to&nbsp;<strong>maintain a steady pace or motion (</strong><strong style=""font-size: 20px;"">Sự vững vàng</strong><span style=""font-size: 20px;"">&nbsp;</span><span style=""font-size: 20px;"">hoặc</span><span style=""font-size: 20px;"">&nbsp;</span><strong style=""font-size: 20px;"">sự đều đặn)</strong></h3></div>
<h3></h3></div><div></div></div>"|""|""
```
  
**MESSAGE TO PROCESS:**
List of new words:
- come to a halt
- kerosene
- quota
- monthly fuel quota
- fortnight
- sennight
- subsidise
- conventional
	- == traditional
- give off: ==
- plight (n): 
- glitch out: 
- throw sb off: 
- perplex

**Current Number: 75**