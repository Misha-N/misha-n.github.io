---
title: "Live from the Bedroom: When We Stream Our Private Lives Online"
date: 2026-04-15 10:00:00 +0200
categories: [Cybersecurity, Privacy]
tags: [iot, cameras, shodan, privacy, osint, cybersecurity, data analysis]  # TAG names should always be lowercase
description: A large-scale analysis of publicly accessible internet-connected cameras using AI and network metadata, uncovering privacy risks, global exposure patterns, and real-world implications of misconfigured IoT devices.
image:
  path: /assets/img/webcams/banner.png
  alt: Visualization of publicly accessible cameras and global privacy exposure with AI analysis overlay.
---

> The materials presented in this article are derived from publicly accessible sources on the internet, including data indexed by services such as Shodan. No authentication bypassing, exploitation, or unauthorized access techniques were used to obtain this information.
>
> All images have been anonymized to remove or obscure any potentially identifiable personal data, in accordance with applicable data protection regulations, including the General Data Protection Regulation (GDPR). The content does not aim to identify any individual, household, or organization.
>
> This article is intended solely for informational, educational, and cybersecurity awareness purposes. The author does not endorse or encourage unauthorized access to systems.
>
> If any individual or entity believes their rights or privacy have been affected, they are encouraged to make contact for immediate review and possible removal of the relevant content.
{: .prompt-warning }

# Live from the Bedroom: When We Stream Our Private Lives Online
Webcams are widely used for everyday purposes such as monitoring homes, checking on pets, supervising workplaces, or observing public spaces. Most of these use cases are completely legitimate. However, not all devices are configured securely, and some end up being publicly accessible without the owner's awareness.

While working with Shodan data, I noticed that screenshots from these webcams appear quite frequently. Looking at a few manually is fine. Looking at thousands quickly turns into a very boring job. And since we live in the AI era, manually browsing tens of thousands of images felt like unnecessary suffering. It made much more sense to let a model do the repetitive work instead.

So I collected publicly accessible webcam screenshots from Shodan. The initial dataset contained **29440 records** with images. During download, some images indicated by the API were missing, suggesting that Shodan performs some filtering or that certain images were no longer available.

## Data cleaning

After downloading, the dataset was cleaned in two stages. First, simple hash-based deduplication was used to remove identical images, reducing the dataset to **26903 unique images**. Since many webcams produce nearly identical frames, a second filtering stage was applied using PIL (Python Imaging Library) and perceptual hashing. Each image was resized to a small resolution, converted to grayscale, and transformed into a small visual fingerprint based on overall shapes and brightness. Similar fingerprints were compared using a small threshold, and near identical images were removed. After filtering, the final dataset contained approximately **21500 unique images**.

## LLM role

Filtered images were then analyzed using a local **Qwen2.5-VL:3B** model running via Ollama. While more capable models exist, this model was selected as a practical tradeoff between performance and processing time given the dataset size. The model was given a simple task:
- Analyze a webcam image
* Determine if image is usable or unusable
	* If unusable → return fixed JSON with all null fields
	* If usable → classify into one predefined place
* Ensure place matches correct category and score
* Extract up to 5 visible object nouns (lowercase, no people descriptions)
* Write short description (max 100 characters)
* Detect sensitive content worth checking and set optional flag
* Return only a valid JSON object
* No explanations, no extra text

With an average analysis time of about **30 to 45 seconds per image**, processing the entire dataset took … a while. Long enough to start browsing new GPUs and pretending it's a necessary investment ... and also wondering why I thought this was a good idea. Anyway, with the analysis finally done, let's explore what the AI discovered.

## Analysis

### Manual data quality check

Before diving into the full dataset, I manually reviewed a small sample of the results. Twenty images were selected from each category, and surprisingly, the LLM was not completely off in any of them. While the sensitivity scoring and object detection could be improved in some cases, there were no major misclassifications. During this validation step, a few technical issues also appeared, such as broken JSON responses or fields placed incorrectly, but given the scale of the dataset, these were expected and relatively minor.

On the other hand, I also realized that my prompt design was probably a bit too restrictive, or simply not formulated in the best way (clearly, my prompt engineering still has room for improvement). This likely influenced the model’s behavior, as the LLM tended to prefer categories and scores that appeared in the examples, a known effect in prompt engineering often referred to as example bias or anchoring.

At the same time, I used a low temperature setting to keep the JSON output stable and avoid formatting issues. While this helped maintain structure, it also made the model more deterministic and less likely to explore alternative classifications. That said, the category lists and examples were still reasonably broad, so the results remain interesting and useful even if the model occasionally played it a bit too safe.

### Dataset overall discovery
In total, about 21500 images were analyzed, with roughly **21000 usable** and around **500 unusable** cases (e.g., blurry, blank, or damaged images). The model identified 141 unique places, flagged 107 images as interesting for further review, and detected 137 dominant unique objects (50241 in total). Overall, most captured scenes leaned toward lower-risk environments while still providing a meaningful portion of higher-sensitivity cases which is exactly the stuff we’re actually hunting for.

![](/assets/img/webcams/10eb978c9d99fe18c16fa2fab7a97555.png)

### Privacy scoring Overview
As mentioned, I used AI-based scene analysis to classify environments and assign a privacy risk score to each image. The idea was simple: outdoor scenes usually mean low risk, while indoor environments, especially workplaces or home areas, tend to increase privacy exposure.

![](/assets/img/webcams/e7ed358383a373506aafe1024d39b2da.png)

*One important note: the AI scoring ended up using only a few specific values. This probably happened because I provided example scores in the prompt, and the model followed them quite literally. Combined with low temperature settings and prompt structure, this resulted in clustered values instead of a smooth distribution. And yes… that one’s on me.*

Next, looking at the privacy score gives a clearer picture. Most cameras fall into the public category, which is expected, many exposed cameras simply show outdoor environments. However, a noticeable portion falls into moderate and highly private categories.

Finally, the second graph shows a simple category breakdown of where these cameras are located. Public outdoor cameras dominate the dataset, followed by indoor and professional environments. This aligns with the privacy scoring.

![](/assets/img/webcams/9886a4c94ec979922f1e234ebc980745.png)
### Geographic Insights
Exposed cameras appear across the globe, confirming that this isn't a regional issue. North America, Europe, and parts of Asia show higher concentrations, likely reflecting higher adoption of connected devices. In short, more cameras = more chances someone forgot to change the default settings.

![](/assets/img/webcams/e987f2743052c1afbd89683b245d1946.png)

![](/assets/img/webcams/e95bfdd5d031130aac7472466f76d3da.png)

Shodan metadata also provides latitude and longitude, so naturally I plotted everything on a world map. Is there a groundbreaking insight here? Not really. Does it look cool? Absolutely.

![](/assets/img/webcams/9edc9321fcac55e1386403151728e84c.png)

Looking at the average privacy score reveals a slightly different picture. Some countries with fewer cameras still show higher average risk. However, using all countries directly produced some… questionable leaders (places with just one or two cameras suddenly appeared as the highest-risk regions). For example, countries like Isle of Man or Oman briefly topped the list, not because they are particularly risky, but simply because they had very few cameras, all of them scoring high. Statistically correct, but not very meaningful. The United Arab Emirates (UAE), however, is an interesting case, with 16 exposed cameras and an average score of 8.

![](/assets/img/webcams/42fb13e421925a93a664c61401700098.png)

To make the results more reliable, I filtered the dataset to include only countries with at least 20 cameras. After applying this filter, a more realistic pattern emerges. Countries such as Singapore, Belarus, Qatar, Egypt, Bangladesh, and China show higher average privacy scores, suggesting a greater proportion of indoor or operational camera environments.

![](/assets/img/webcams/44fa06525640fbd96b014f566ec35f93.png)

Focusing only on high-privacy cameras (score ≥ 8) reveals where the most sensitive exposures are concentrated. The United States leads by a noticeable margin, followed by Russia, Italy, Japan, and Vietnam. Other countries with significant numbers include Taiwan, Thailand, Germany, Mexico, and France.

![](/assets/img/webcams/7b9b29ef7f77e497f8a6217bb34bc88f.png)

At the city level, Tokyo stands out by a large margin, followed by Ho Chi Minh City, Taichung, and Taipei. Several Japanese cities also appear, indicating higher camera density. European and US cities such as Paris, Rome, Milan, New York City, and Chicago appear with smaller counts. Large cities naturally mean more cameras and occasionally more things unintentionally shared with the internet.

![](/assets/img/webcams/667e216062568a8a79f5cb67f27a39b7.png)

### Scene Insights
The most common camera locations are streets and parks, which explains why the majority of cameras fall into the lower privacy risk categories. These environments are typically public and expected to be monitored. However, the dataset also includes office desks, parking lots, living rooms, retail stores, and bedrooms, which quickly shifts the conversation from public infrastructure to potential privacy concerns. Notably, the presence of home environments (living rooms, bedrooms, kitchens) suggests that some cameras may not have been intended for public access at all.

![](/assets/img/webcams/839781404a90cc688cbbfc472306a0fc.png)

Across all images, the AI detected 50241 objects with 137 unique types. Object detection further confirms the dominance of outdoor scenes, with cars, sidewalks, trees, and grass leading the results. These are typical for street and park cameras.

However, indoor-related objects also appear frequently, including chairs, desks, monitors, beds, counters, and shelves. These objects indicate indoor environments such as offices, homes, and retail spaces, which generally carry higher privacy implications.

The presence of monitors, desks, and counters is particularly interesting, as these often correspond to workspaces or operational areas.

![](/assets/img/webcams/5d7f406ccbd824406796725ea59ab3b0.png)

The word clouds below show object frequency (larger words mean more occurrences. This provides a quick overview of what publicly accessible cameras are actually capturing).

![](/assets/img/webcams/50346df2878f9a05efb774d386cc6947.png)

Another useful source for understanding what cameras capture is the AI-generated scene descriptions. After removing common stopwords (like "a", "the", "with", "and", etc.), I generated another word cloud from these descriptions.

Here the AI gets noticeably more creative (about 1800 unique words, 84800 in total). Since descriptions combine objects, places, and context, the vocabulary becomes much richer. Same cameras, just with a bit more storytelling.

![](/assets/img/webcams/c4a9a963870a4262a7be9b6d395b427e.png)

Among the rarest words, things got surprisingly weird. The AI occasionally mentioned pirate, cowboy, comet, octopus, shark, hedgehog, ducks, and bears ... which sounds less like security cameras and more like a very confused nature documentary. Some words were unexpectedly dramatic, like bloodstains, surgery, guns, checkpoint, police, syringe, cemetery, gravestones ... words that definitely make you pause and wonder what exactly the camera saw. One wonders whether the AI caught something unusual… or just got a little too creative.

To better understand scene context, I also analyzed which objects appear together in images. This network graph shows relationships between objects where the more visible connections, the more frequently they co-occur. Unsurprisingly, common outdoor elements like road, building, tree, car, and sidewalk form the "central cluster", appearing together across many cameras. 

![](/assets/img/webcams/88795c4dbc5a760cc0aaedb87659bab1.png)

Well, the previous graph isn't exactly ideal, and it's a bit of a mess. So to uncover natural groupings, I applied the Louvain community detection algorithm to the object co-occurrence network. In simple terms, it groups objects that appear together more often than expected. Or in other words letting the data decide what belongs with what, instead of me guessing.

The algorithm mentioned above also allowed me to use more objects without the bubbles overlapping, and I didn't have to struggle with fine-tuning the ideal "attraction force" between individual objects. Using the top 300 most frequent objects, several clear communities emerged. One cluster groups indoor household items like furniture, appliances, and electronics. Another captures outdoor nature scenes with trees, sky, water, and terrain. A separate cluster represents urban street environments like cars, sidewalks, signs, and roads, while another groups industrial and construction equipment such as tools and machinery. A smaller cluster even isolates farm-related objects, because apparently some cameras are keeping an eye on cows too.

Also, the clusters aren’t perfectly separated, lots of connections exist between them. Which makes sense, because real life is messy. Sometimes you get a street next to a park next to construction equipment… and occasionally, probably a cow.

![](/assets/img/webcams/e196c4ea543e53bf6aea6903a775ada1.png)

## Gallery of Notable Images
At this point, we’ve explored the data from quite a few angles ... and the chart count is starting to rival the number of cameras themselves. Rather than adding more graphs, it’s a good moment to step back and highlight some of the most interesting (and occasionally concerning) images found during the analysis.
To be honest, a few of these images sit on the darker side of what you’d expect from publicly accessible cameras. In some cases, I even questioned whether they should be included at all. A small number of images were intentionally left out. Not because they weren’t interesting, but because they crossed a line where showing them would defeat the purpose of responsible research.
However, one of the main motivations behind this research is raising awareness. These examples highlight how easily cameras can expose private spaces or sensitive situations when deployed without proper security considerations. The goal here isn’t to sensationalize, but to raise awareness that when IoT cameras are deployed without proper security considerations, they can quietly turn private moments into public ones ...

On a more serious note, many of the cameras in the following gallery were clearly deployed with good intentions like monitoring property, pets, children, elderly family members, or people with disabilities. However, due to weak or missing security, these same cameras were publicly accessible to anyone. In several cases, they were placed in highly sensitive environments such as bedrooms, bathrooms, hospitals, care facilities, and kindergartens. This highlights a critical privacy risk, when security is overlooked, tools meant to increase safety can unintentionally expose the very people they were meant to protect.

![](/assets/img/webcams/43ceb23f26b137ffedba6491e9de38ea.png)

![](/assets/img/webcams/461bf35f05224c44851e722039956c02.png)

![](/assets/img/webcams/7bb9f94cda5c9eaa1dfc9a37468c54d4.png)

![](/assets/img/webcams/5d819b46f7ed87d202ef6fb5ab8852ca.png)

![](/assets/img/webcams/8546c63fa62a489fc846cff5f3b5020d.png)

![](/assets/img/webcams/5902de850c68646cb6b6130764bb7fdc.png)

![](/assets/img/webcams/525899c308315715e6a75875d4412a1f.png)

![](/assets/img/webcams/678cf6ce7bc54e8ed60e459d8e621985.png)

![](/assets/img/webcams/4f178c22a3b53583e224d9ab01f37461.png)

![](/assets/img/webcams/5cb03feb5721e8e13ea0bac216789e80.png)

![](/assets/img/webcams/0b32ff7d255eb7c91cedf441ce8046cd.png)

![](/assets/img/webcams/f77c3a836df793ca0ae63071ccfc16eb.png)

![](/assets/img/webcams/6a91e520d0fdfb7e99bef68e4392fdc9.png)

![](/assets/img/webcams/847105efb8933c5e2e7f498f1c70e386.png)

![](/assets/img/webcams/f64b656ea119f2ce4788e18ecf586973.png)

![](/assets/img/webcams/b7cc351fd996bac40e17e869ecbe4f43.png)

![](/assets/img/webcams/9e3ff3a53f13e719ed45904430437560.png)

![](/assets/img/webcams/9e34d2d550107ff932fcd9ebd08226a7.png)

![](/assets/img/webcams/8e71bdc78cf2aff3a7c3ba90b51d67e7.png)

![](/assets/img/webcams/36dd3993d8a44ca2219c8bd75a3fa1c1.png)

![](/assets/img/webcams/5996d8428d8caabe5e737c5d7ecdf2d9.png)

![](/assets/img/webcams/72cd935aa72a5210f949efea110b52db.png)

![](/assets/img/webcams/a23ceafa668dedffeceb5b73efec0892.png)

![](/assets/img/webcams/420d0b8f81f20ad01cb18534ee946b49.png)

![](/assets/img/webcams/5dfbf20a83d0f2421bfe46fac0a28621.png)

![](/assets/img/webcams/d77f9af09ed3d51a5429fc23f61104a7.png)

![](/assets/img/webcams/453d26f72c3a69fe525ed8aaee484dbb.png)

![](/assets/img/webcams/eb11542f92bd75a98f5bb0dba9de19e0.png)

![](/assets/img/webcams/a88adbe652a883c8166aac4dd6216031.png)

![](/assets/img/webcams/1e282a2ef178b8591999b9e374b60903.png)

![](/assets/img/webcams/d798ebfd8364e363bffd5d06e5ce850b.png)

![](/assets/img/webcams/2779834b05750194bd06f1d918495ea3.png)

![](/assets/img/webcams/b8074e426fc507dc7746c44fc40571d2.png)

![](/assets/img/webcams/707f2ec9ea637ad22c11c75682ad1f58.png)

![](/assets/img/webcams/55952456c5e4be1ba836f619e0207e57.png)

![](/assets/img/webcams/13f795a5775a5a8b3f7aff9ffcbf4027.png)

![](/assets/img/webcams/ef90fe0d3dd37ef9346d301cfead6808.png)

![](/assets/img/webcams/e371b2a7ab69bb96bb3045fae2c274e9.png)

![](/assets/img/webcams/31e02ad43ecbb28617ae7c45ac1fb576.png)

![](/assets/img/webcams/247b6296b12f6bf0c0742d7b0bd88be7.png)

![](/assets/img/webcams/2fb8960e7c8776d81dd4ec77cc39d3e2.png)

![](/assets/img/webcams/8026c22e5d909a81da8ef4923c572fc6.png)

## Conclusion
This analysis shows that publicly accessible cameras are not rare edge cases, they are widespread, global, and often unintentionally exposed. Most cameras capture harmless scenes, but a meaningful subset reveals environments that were clearly not meant to be public. The issue is not the technology itself, but how it is deployed.

From a cybersecurity perspective, exposed IoT cameras are a classic example of convenience outweighing security. Default settings, weak authentication, and direct internet exposure create a situation where devices designed for safety can quietly become sources of privacy risk.
### Practical Recommendations
To reduce exposure, a few fundamental practices make a significant difference:
- **Never expose cameras directly to the internet**: use VPNs or secure gateways instead of public access.
- **Change default credentials immediately**: default usernames and passwords are one of the most common causes of exposure.
- **Use network segmentation**: place IoT devices on separate networks (VLANs) from critical systems.
- **Enable authentication and encryption**: avoid unauthenticated streams (e.g., open RTSP).
- **Regularly update firmware**: many devices run outdated software with known vulnerabilities.
- **Disable unused services and ports**: reduce the attack surface as much as possible.
- **Monitor access logs if available**: even basic visibility helps detect unusual activity.
    
Ultimately, securing IoT devices (including cameras) is not about advanced techniques, but about getting the basics right. Small configuration changes can be the difference between a private system and one that is accessible to anyone on the internet.