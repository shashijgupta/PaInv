# Machine Translation Testing via Pathological Invariance

Translation errors found by our approach in [Google Translate](https://translate.google.com/) and [Bing Microsoft Translator](https://www.bing.com/translator). 

we introduce a novel methodology called PatInv for validating machine translation software. The main intuition behind PatInv is that sentences with different meanings should not have the same translations. Hence, we find pair of sentences with different meanings but same translations. PatInv is able to find many translation errors in on two language settings (Eng->Hindi and Eng->Chinese). We will keep updating the erroneous translations uncovered by PatInv to this repo. A pathological invariant is a pair of sentences that are of different meanings but obtain the same translation. We use "pathological invariant" and "erroneous issue" interchangeably in the paper.

### Environment for running the code:
To use Google Translate API and Bing Microsoft Translator API, you need to register corresponding accounts.
+ For Google Translate, useful tips could be found here: [\[G1\]](https://cloud.google.com/translate/docs/basic/setup-basic)[\[G2\]](https://neliosoftware.com/blog/how-to-generate-an-api-key-for-google-translate/?nab=0&utm_referrer=https%3A%2F%2Fwww.google.com%2F). 
+ For Bing Microsoft Translator, after getting your API key string, you need to copy that into the [PaInv code](./PaInv.py#L171). Useful tips: [\[B1\]](https://azure.microsoft.com/en-us/services/cognitive-services/translator-text-api/)[\[B2\]](https://docs.microsoft.com/en-us/azure/cognitive-services/translator/translator-text-how-to-signup)

To set up BERT, you could refer to Hugging Face's [implementation](https://github.com/huggingface/transformers) in PyTorch.

To set up language parsers:
+ Download the [core NLP libarary](http://nlp.stanford.edu/software/stanford-corenlp-full-2018-10-05.zip).
+ Unzip the file and open the folder.
+ Run `java -Xmx4g -cp "*" edu.stanford.nlp.pipeline.StanfordCoreNLPServer -serverProperties StanfordCoreNLP-chinese.properties -preload tokenize,ssplit,pos,lemma,ner,parse,depparse -status_port 9001  -port 9001 -timeout 15000`

If you want to filter using sentence embeddings, run [install_USE.sh](./install_USE.sh#L171) before running the main code. This will install [universal sentence encoder](https://tfhub.dev/google/universal-sentence-encoder/1) in your machine.


### Template in the translation error examples: :telescope: 
+ Row 1: source sentence (in English)
+ Row 2: new generated sentence (in English)
+ Row 3: Their same translation(in either Hindi or Chinese)
+ Row 4: Which sentence has translation error (either of first, second or both)
+ Row 5: Meaning of their common translation in english.

### Input and output example
The input of PatInv is a list of unlabeled sentences in source language. For clarity, we use an unlabeled sentence here as an example.

||
| :------------- | 
| They are doing something completely different. | 
||

The output of PatInv is a list of issues.

An example output of PatInv for English->Hindi language setting is as follows. 

| Issue 1 |
| :------------- | 
| They are doing something completely different. |
| They are doing something slightly different. | 
| वे कुछ अलग कर रहे हैं। |
| both |
| They are doing something different. | 

| Issue 2 |
| :------------- | 
| They are doing something completely different. |
| They are doing something different. | 
| वे कुछ अलग कर रहे हैं। |
| first |
| They are doing something slightly different. | 

As we can see, PatInv reports two suspicious issues. These two issues are genearated using two different approaches. Issue 1 is generated by perturbing a word in a sentence, whereas issue 2 is generated by removing word/phrase from a sentence.


### Examples of translation errors detected by <em>PatInv</em>

### Google Translate

For English->Hindi Setting:

| Issue 1 |
| :--- |
| I am very willing to share my point of view. |
| I am very willing to share my point of day. |
| मैं अपनी बात साझा करने के लिए बहुत इच्छुक हूं। |
| both |
| I am very willing to share my point. |

| Issue 2 |
| :--- |
| I had a story to tell and I wanted to finish it, Draper says. |
| I had a story to tell and I wanted to finish it, jack says. |
| मेरे पास बताने के लिए एक कहानी थी और मैं इसे खत्म करना चाहता था। |
| both |
| I had a story to tell and I wanted to finish it. |

| Issue 3 |
| :--- |
| The key to accepting praise at work is to show you received it and appreciate it. |
| The key to accepting praise at work is to think you received it and appreciate it. |
| काम पर प्रशंसा स्वीकार करने की कुंजी यह है कि आप इसे प्राप्त करें और इसकी सराहना करें। |
| both |
| The key to accepting praise at work is to receive it and appreciate it. |

| Issue 4 |
| :--- |
| The situation at the southern border, which started as a crisis, is now a near system wide meltdown. |
| The situation at the southern border, which started as a crisis, is now a near system wide development. |
| दक्षिणी सीमा पर स्थिति, जो एक संकट के रूप में शुरू हुई थी, अब एक निकटवर्ती प्रणाली है। |
| second |
| The situation at the southern border, which started as a crisis, is now a nearby system. |

| Issue 5 |
| :--- |
| It ' s not just a matter of sending money their way. |
| It ' s not just a matter of sending money . |
| यह सिर्फ पैसे भेजने की बात नहीं है। |
| first |
| It ' s not just a matter of sending money . |

:point_right: More translation errors in Google Translate detected by <em>PatInv</em> for English->Hindi setting can be found [here](./errors/errorsgooglehindi.md).

For English->Chinese Setting:

| Issue 1 |
| :--- |
| I am very willing to share my point of view. |
| I am very willing to share my point of wisdom. |
| 我非常愿意分享我的观点。 |
| second |
| I am very willing to share my point of view. |

| Issue 2 |
| :--- |
| Before, economists used to work on public data. |
| Before, economists used to look on public data. |
| 以前，经济学家曾经研究过公共数据。 |
| second |
| Before, economists used to work on public data. |

| Issue 3 |
| :--- |
| Other tech companies that also make effective use of economists — like Uber, which has a 30 person team — speak with frank admiration of the apparatus Amazon has built. |
| Other tech companies that also make effective use of economists — like Uber, which has a 30 men team — speak with frank admiration of the apparatus Amazon has built. |
| 其他也有效利用经济学家的科技公司，例如拥有30人团队的优步（Uber），坦率地钦佩亚马逊制造的设备。 |
| second |
| Other tech companies that also make effective use of economists — like Uber, which has a 30 person team — speak with frank admiration of the apparatus Amazon has built. |

| Issue 4 |
| :--- |
| The South has emerged as a hub of new auto manufacturing by foreign makers thanks to lower manufacturing costs and less powerful unions. |
| The South has died as a hub of new auto manufacturing by foreign makers thanks to lower manufacturing costs and less powerful unions. |
| 由于较低的制造成本和较弱的工会，南方已经成为外国制造商新汽车制造的枢纽。 |
| both |
| The South has become a hub of new auto manufacturing by foreign makers thanks to lower manufacturing costs and less powerful unions. |

| Issue 5 |
| :--- |
| Trump has not repaired his relationship with Bannon after a bitter split, people familiar with the two men say. |
| Trump has not repaired his relationship with Bannon after a bitter split, people familiar say. |
| 知情人士说，在痛苦的分裂之后，特朗普没有恢复与班农的关系。 |
| first |
| Trump has not repaired his relationship with Bannon after a bitter split, people familiar say. |

:point_right: More translation errors in Google Translate detected by <em>PatInv</em> for English->Chinese setting can be found [here](./errors/errorsgooglechinese.md).


### Bing Microsoft Translator

For English->Hindi setting:

| Issue 1 |
| :--- |
| First, proper training for pilots who are flying new aircraft is crucially important. |
| First, proper training for pilots who are flying new aircraft is important. |
| पहला, नए विमान उड़ाने वाले पायलटों के लिए उचित प्रशिक्षण महत्वपूर्ण है । |
| first |
| First, proper training for pilots who are flying new aircraft is important. |

| Issue 2 |
| :--- |
| A simple response of thank you very much, I appreciate you saying that or thank you, I worked very hard on it can suffice. |
| A simple response of thank you very much, I appreciate you saying that or thank you, I worked very hard on it can . |
| आप बहुत बहुत धन्यवाद की एक साधारण प्रतिक्रिया, मैं आपको यह कहते हुए सराहना करता हूं कि या धन्यवाद, मैंने इस पर बहुत मेहनत की है। |
| first |
| A simple response of thank you very much, I appreciate you saying that or thank you, I worked very hard on it. |

| Issue 3 |
| :--- |
| I am very willing to share my point of view. |
| I am very willing to share my areas of view. |
| मैं अपने दृष्टिकोण को साझा करने के लिए बहुत इच्छुक हूं । |
| second |
| I am very willing to share my areas of view. |

| Issue 4 |
| :--- |
| But only 6% of them attend private institutions, and though the elite University of California system has significantly increased its Latino enrollment, just 4% attend one of those campuses. |
| But only 6% of them attend private institutions, and though the elite University of California system has vastly increased its Latino enrollment, just 4% attend one of those campuses. |
| लेकिन उनमें से केवल 6% निजी संस्थानों में भाग लेते हैं, और हालांकि कैलिफोर्निया प्रणाली के अभिजात वर्ग विश्वविद्यालय काफी अपने लातीनी नामांकन में वृद्धि हुई है, सिर्फ 4% उन परिसरों में से एक में भाग लेते हैं । |
| first |
| But only 6% of them attend private institutions, and though the elite University of California system has vastly increased its Latino enrollment, just 4% attend one of those campuses. |

| Issue 5 |
| :--- |
| And he pointed out that Toyota, the Japanese company, is investing in the US by opening new plants. |
| And he told out that Toyota, the Japanese company, is investing in the US by opening new plants. |
| और उन्होंने बताया कि जापानी कंपनी टोयोटा नए प्लांट खोलकर अमेरिका में निवेश कर रही है। |
| first |
| And he told that Toyota, the Japanese company, is investing in the US by opening new plants. |


:point_right: More translation errors in Bing Microsoft Translator detected by <em>PatInv</em> for Eng->Hindi setting can be found [here](./errors/errorsbinghindi.md).


For English->Chinese Setting:

| Issue 1 |
| :--- |
| Deutsche Bank investors are running out of patience. |
| Deutsche game investors are running out of patience. |
| 德意志银行的投资者已经没有耐心了。 |
| both |
| Deutsche Bank investors are out of patience. |

| Issue 2 |
| :--- |
| I am very willing to share my point of view. |
| I am very willing to share my mission of view. |
| 我非常愿意分享我的观点。 |
| second |
| I am very willing to share my point of view. |

| Issue 3 |
| :--- |
| At its peak in 2014, the blood testing startup was a darling of Silicon Valley. |
| At its peak in 2014, the blood testing startup was a darling of Silicon county. |
| 在2014年达到顶峰时，这家血液检测初创公司是硅谷的宠儿。 |
| second |
| At its peak in 2014, the blood testing startup was a darling of Silicon Valley. |

| Issue 4 |
| :--- |
| You could interview for lots of jobs at the same time, said Fairweather, who left Amazon in 2018 to become the chief economist for the real estate website Redfin. |
| You could interview for lots of jobs at the same time, said Fairweather, who left Amazon in 2018 to the chief economist for the real estate website Redfin. |
| 费尔韦瑟说，你可以同时面试很多工作，他于2018年离开亚马逊，成为房地产网站Redfin的首席经济学家。 |
| second |
| You could interview for lots of jobs at the same time, said Fairweather, who left Amazon in 2018 to become the chief economist for the real estate website Redfin. |

| Issue 5 |
| :--- |
| The threatened tariffs led to the European Union pledging countertariffs. |
| The threatened tariffs led to the Union pledging countertariffs. |
| 威胁的关税导致欧盟承诺反关税。 |
| second |
| The threatened tariffs led to the European Union pledging countertariffs. |

:point_right: More translation errors in Bing Microsoft Translator detected by <em>PatInv</em> for English->Chinese setting can be found [here](./errors/errorsbingchinese.md).
