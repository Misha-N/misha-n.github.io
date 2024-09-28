---
title: Caught in the Easter Trap: SAZKA SMShing Attack
date: 2024-09-28 13:21:00 +0200
categories: [Cybersecurity, Phishing]
tags: [smshing, phishing, sazka, cybersecurity, fraud]  # TAG names should always be lowercase
description: An analysis of SMShing attack that mimicked the SAZKA website, targeting customers with a fake Easter lottery scam.
---

I recently came across a fairly well-made SMShing attack where the attackers faithfully mimicked the website of SAZKA, a Czech company mainly running number games and betting. The attackers took advantage of the company's advertising campaign promoting limited Easter scratch tickets and targeted the company's customers.

In this attack, the attackers sent an SMS message to the victim's phone containing a link to a website. In SMS victim was offered a free lottery ticket to celebrate Easter and the arrival of spring with a top prize of up to 2 million CZK, which is a classic baiting technique used by attackers to lure victims into clicking on links.

![Delivered SMShing message](/assets/img/easter_trap/sms1.jpg){: w="700" h="400" }
_Delivered SMShing message_

![Detail of SMShing message](/assets/img/easter_trap/sms2.jpg){: w="700" h="400" }
_Detail of SMShing message_

Once the victim clicked on the link, they were taken to a website that looked just like the legitimate SAZKA's website. The attackers were able to copy the website faithfully, which made it difficult for the victim to distinguish between the real and fake site. They even registered the look-alike domain sazka[.]pw.

![Fraudulent webpage](/assets/img/easter_trap/main_page2.png){: w="700" h="400" }
_Fraudulent webpage_

However, most of the links on the site led either to a fake payment gateway or a form requesting sensitive data such as an address. The fake payment gateway demanded payment of 1 CZK.

![Payment page](/assets/img/easter_trap/payment.png){: w="700" h="400" }
_Payment page_

![Shipping Information Form](/assets/img/easter_trap/form.png){: w="700" h="400" }
_Shipping Information Form_

What makes this attack even more interesting is that after the victim entered their card details, the fraudulent payment confirmation page was shown and asked for a confirmation code, which could come to the victim, for example, in a text message to their mobile phone. If the victim was using a code sent via SMS to confirm payments, attackers would be able to bypass this second payment confirmation factor.

![Payment confirmation](/assets/img/easter_trap/confirmation.png){: w="700" h="400" }
_Payment confirmation_

![Payment confirmation code](/assets/img/easter_trap/confirmation2.png){: w="700" h="400" }
_Payment confirmation code_

Overall, this SMShing attack is an excellent example of how attackers are becoming more sophisticated in their techniques to deceive victims into divulging sensitive information. It is also an interesting coincidence that the victim who received the SMShing is indeed actively using the SAZKA websites. It is important to be aware of these types of attacks and to take precautions to protect your personal and financial information and verify the legitimacy of a website before accessing it or entering any data.

