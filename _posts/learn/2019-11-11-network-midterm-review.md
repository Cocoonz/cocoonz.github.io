---
layout: post
title: 《计算机网络：自顶向下方法》学习笔记（更新中）
category: 学习
tags: computer science
description: 
---

$a\neq b$ is inline


$$
display \cdot math \cdot block
$$


\[

this is a display math block

\]



\$inline math block\$



## Chapter 2 Application Layer

### 网络应用架构（Network application architectures）

由应用开发者设计，决定了应用在不同终端上的结构

1. C/S 架构（client-server architecture）

   - 特点：
     - 一台常开主机（always-on host）（服务器）响应来自其他主机（客户机）的请求
     - 客户机之间互相不能直接通信
     - server 具有固定的、为人熟知的 IP 地址

   - 数据中心（data center）：多台主机构成的虚拟服务器（virtual server），解决单台 server 不能满足应用需求的问题
   - 应用：Web、FTP、Telnet、e-mail

2. P2P 架构（peer-to-peer architecture）
   - 特点：
     - 没有专门 server，主机之间直接通信
     - 良好的自扩展性（self-scalability）
     - 高效（efficient），因为不需要服务器架构，也没有带宽限制
   - 面临的挑战：
     - ISP friendly，目前大多数 ISP 提供的服务都是上下行速度不对称的，P2P 的方式给 ISP 带来了很大压力
     - 安全性
     - 动机（incentive）设计，鼓励用户贡献带宽等资源
   - 应用：BitTorrent、迅雷、PPTV



### 进程通信（Processes Communicating）

1. 同一终端上的进程通信：进程间通信（interprocess communication），由操作系统管理

2. 不同终端上的进程通信：通过网络交换信息

   - client process：发起通信的一方，server precess：等待通信的一方

   - **Socket**：进程与网络的软件交界（software interface），是同一台主机上**应用层和网络层的交界**。（如果把 process 比作房子，那么 socket 就是房子的大门）

     > a socket is the interface between the application layer and the transport
     > layer within a host.

   - socket 也被称为应用层和网络层之间的应用编程接口（Application Programming Interface），应用开发者对于应用层的部分有控制权，但对网络层部分没有什么权限

   ![](https://s2.ax1x.com/2019/11/11/MlJ80I.png)

3. 进程寻址（Process Addressing）

   进程地址由两部分信息组成：

   - 主机地址（the address of host）：即 32 位 IP 地址

   - 目标进程标识符（an identifier that specifies the receiving process in the destination host），或者说是目标进程的 socket，即端口号（port number）

   - 一些主流应用被分配了固定的端口号：web server 80，mail server 25

     > 常用端口号	http://www.iana.org



### 传输层协议为应用提供的服务（Transport Services Available to Applications）  

1. 可靠数据传输（reliable data transfer）

   - 一些应用需要完整无误的数据传输，如电子邮件、文件传输等

   - loss-tolerant application：可以接受传输过程中数据丢失，如多媒体应用

2. 吞吐量（throughput）

   - 可用带宽（available throughput）：发送进程传输数据给接收进程的速度
   - 带宽保证（throughput guarantees）：保证带宽达到某一水平
   - 带宽敏感性应用（bandwith-sensitive applications）：有最小带宽要求，如当前很多多媒体应用
   - 可伸缩应用（elastic application）：自适应带宽，如电子邮件、文件传输等

3. 时间（timing）

   实时应用如电话、电子会议、多人游戏等对延时要求较高

4. 安全性（security）

   发送端和接收端的加解密、数据完整性（data integrity）、末尾校验（end-point authentication）



### 因特网提供的传输服务（ Transport Services Provided by the Internet ）

1. TCP

   - 在应用层可以通过 SSL 加密

   - 面向连接（connection-oriented）的服务：开始通信前需要通过握手建立两个应用 socket 之间的 TCP 连接（TCP connection），完成通信后需要断开连接
   - 可靠数据传输
   - 拥塞控制机制（congestion control mechanism）：当网络拥塞时对发送端节流

2. UDP

   - 无需建立连接
   - 数据传输不可靠
   - 没有拥塞控制

3. 今天的因特网虽然可以为时间敏感的应用提供满意的服务，但是它仍不能提供对吞吐量和延时限制的保证

   ![page0](https://s2.ax1x.com/2019/11/12/M18wfU.png) 

<svg xmlns:xlink="http://www.w3.org/1999/xlink" width="46.104ex" height="2.605ex" viewBox="0 -790.8 19850.2 1121.5" role="img" focusable="false" style="vertical-align: -0.768ex;"><defs><path stroke-width="0" id="E1-MJMATHI-62" d="M73 647Q73 657 77 670T89 683Q90 683 161 688T234 694Q246 694 246 685T212 542Q204 508 195 472T180 418L176 399Q176 396 182 402Q231 442 283 442Q345 442 383 396T422 280Q422 169 343 79T173 -11Q123 -11 82 27T40 150V159Q40 180 48 217T97 414Q147 611 147 623T109 637Q104 637 101 637H96Q86 637 83 637T76 640T73 647ZM336 325V331Q336 405 275 405Q258 405 240 397T207 376T181 352T163 330L157 322L136 236Q114 150 114 114Q114 66 138 42Q154 26 178 26Q211 26 245 58Q270 81 285 114T318 219Q336 291 336 325Z"></path><path stroke-width="0" id="E1-MJMATHI-61" d="M33 157Q33 258 109 349T280 441Q331 441 370 392Q386 422 416 422Q429 422 439 414T449 394Q449 381 412 234T374 68Q374 43 381 35T402 26Q411 27 422 35Q443 55 463 131Q469 151 473 152Q475 153 483 153H487Q506 153 506 144Q506 138 501 117T481 63T449 13Q436 0 417 -8Q409 -10 393 -10Q359 -10 336 5T306 36L300 51Q299 52 296 50Q294 48 292 46Q233 -10 172 -10Q117 -10 75 30T33 157ZM351 328Q351 334 346 350T323 385T277 405Q242 405 210 374T160 293Q131 214 119 129Q119 126 119 118T118 106Q118 61 136 44T179 26Q217 26 254 59T298 110Q300 114 325 217T351 328Z"></path><path stroke-width="0" id="E1-MJMATHI-6E" d="M21 287Q22 293 24 303T36 341T56 388T89 425T135 442Q171 442 195 424T225 390T231 369Q231 367 232 367L243 378Q304 442 382 442Q436 442 469 415T503 336T465 179T427 52Q427 26 444 26Q450 26 453 27Q482 32 505 65T540 145Q542 153 560 153Q580 153 580 145Q580 144 576 130Q568 101 554 73T508 17T439 -10Q392 -10 371 17T350 73Q350 92 386 193T423 345Q423 404 379 404H374Q288 404 229 303L222 291L189 157Q156 26 151 16Q138 -11 108 -11Q95 -11 87 -5T76 7T74 17Q74 30 112 180T152 343Q153 348 153 366Q153 405 129 405Q91 405 66 305Q60 285 60 284Q58 278 41 278H27Q21 284 21 287Z"></path><path stroke-width="0" id="E1-MJMATHI-64" d="M366 683Q367 683 438 688T511 694Q523 694 523 686Q523 679 450 384T375 83T374 68Q374 26 402 26Q411 27 422 35Q443 55 463 131Q469 151 473 152Q475 153 483 153H487H491Q506 153 506 145Q506 140 503 129Q490 79 473 48T445 8T417 -8Q409 -10 393 -10Q359 -10 336 5T306 36L300 51Q299 52 296 50Q294 48 292 46Q233 -10 172 -10Q117 -10 75 30T33 157Q33 205 53 255T101 341Q148 398 195 420T280 442Q336 442 364 400Q369 394 369 396Q370 400 396 505T424 616Q424 629 417 632T378 637H357Q351 643 351 645T353 664Q358 683 366 683ZM352 326Q329 405 277 405Q242 405 210 374T160 293Q131 214 119 129Q119 126 119 118T118 106Q118 61 136 44T179 26Q233 26 290 98L298 109L352 326Z"></path><path stroke-width="0" id="E1-MJMATHI-77" d="M580 385Q580 406 599 424T641 443Q659 443 674 425T690 368Q690 339 671 253Q656 197 644 161T609 80T554 12T482 -11Q438 -11 404 5T355 48Q354 47 352 44Q311 -11 252 -11Q226 -11 202 -5T155 14T118 53T104 116Q104 170 138 262T173 379Q173 380 173 381Q173 390 173 393T169 400T158 404H154Q131 404 112 385T82 344T65 302T57 280Q55 278 41 278H27Q21 284 21 287Q21 293 29 315T52 366T96 418T161 441Q204 441 227 416T250 358Q250 340 217 250T184 111Q184 65 205 46T258 26Q301 26 334 87L339 96V119Q339 122 339 128T340 136T341 143T342 152T345 165T348 182T354 206T362 238T373 281Q402 395 406 404Q419 431 449 431Q468 431 475 421T483 402Q483 389 454 274T422 142Q420 131 420 107V100Q420 85 423 71T442 42T487 26Q558 26 600 148Q609 171 620 213T632 273Q632 306 619 325T593 357T580 385Z"></path><path stroke-width="0" id="E1-MJMATHI-69" d="M184 600Q184 624 203 642T247 661Q265 661 277 649T290 619Q290 596 270 577T226 557Q211 557 198 567T184 600ZM21 287Q21 295 30 318T54 369T98 420T158 442Q197 442 223 419T250 357Q250 340 236 301T196 196T154 83Q149 61 149 51Q149 26 166 26Q175 26 185 29T208 43T235 78T260 137Q263 149 265 151T282 153Q302 153 302 143Q302 135 293 112T268 61T223 11T161 -11Q129 -11 102 10T74 74Q74 91 79 106T122 220Q160 321 166 341T173 380Q173 404 156 404H154Q124 404 99 371T61 287Q60 286 59 284T58 281T56 279T53 278T49 278T41 278H27Q21 284 21 287Z"></path><path stroke-width="0" id="E1-MJMATHI-74" d="M26 385Q19 392 19 395Q19 399 22 411T27 425Q29 430 36 430T87 431H140L159 511Q162 522 166 540T173 566T179 586T187 603T197 615T211 624T229 626Q247 625 254 615T261 596Q261 589 252 549T232 470L222 433Q222 431 272 431H323Q330 424 330 420Q330 398 317 385H210L174 240Q135 80 135 68Q135 26 162 26Q197 26 230 60T283 144Q285 150 288 151T303 153H307Q322 153 322 145Q322 142 319 133Q314 117 301 95T267 48T216 6T155 -11Q125 -11 98 4T59 56Q57 64 57 83V101L92 241Q127 382 128 383Q128 385 77 385H26Z"></path><path stroke-width="0" id="E1-MJMATHI-68" d="M137 683Q138 683 209 688T282 694Q294 694 294 685Q294 674 258 534Q220 386 220 383Q220 381 227 388Q288 442 357 442Q411 442 444 415T478 336Q478 285 440 178T402 50Q403 36 407 31T422 26Q450 26 474 56T513 138Q516 149 519 151T535 153Q555 153 555 145Q555 144 551 130Q535 71 500 33Q466 -10 419 -10H414Q367 -10 346 17T325 74Q325 90 361 192T398 345Q398 404 354 404H349Q266 404 205 306L198 293L164 158Q132 28 127 16Q114 -11 83 -11Q69 -11 59 -2T48 16Q48 30 121 320L195 616Q195 629 188 632T149 637H128Q122 643 122 645T124 664Q129 683 137 683Z"></path><path stroke-width="0" id="E1-MJMATHI-63" d="M34 159Q34 268 120 355T306 442Q362 442 394 418T427 355Q427 326 408 306T360 285Q341 285 330 295T319 325T330 359T352 380T366 386H367Q367 388 361 392T340 400T306 404Q276 404 249 390Q228 381 206 359Q162 315 142 235T121 119Q121 73 147 50Q169 26 205 26H209Q321 26 394 111Q403 121 406 121Q410 121 419 112T429 98T420 83T391 55T346 25T282 0T202 -11Q127 -11 81 37T34 159Z"></path><path stroke-width="0" id="E1-MJMATHI-6C" d="M117 59Q117 26 142 26Q179 26 205 131Q211 151 215 152Q217 153 225 153H229Q238 153 241 153T246 151T248 144Q247 138 245 128T234 90T214 43T183 6T137 -11Q101 -11 70 11T38 85Q38 97 39 102L104 360Q167 615 167 623Q167 626 166 628T162 632T157 634T149 635T141 636T132 637T122 637Q112 637 109 637T101 638T95 641T94 647Q94 649 96 661Q101 680 107 682T179 688Q194 689 213 690T243 693T254 694Q266 694 266 686Q266 675 193 386T118 83Q118 81 118 75T117 65V59Z"></path><path stroke-width="0" id="E1-MJMATHI-65" d="M39 168Q39 225 58 272T107 350T174 402T244 433T307 442H310Q355 442 388 420T421 355Q421 265 310 237Q261 224 176 223Q139 223 138 221Q138 219 132 186T125 128Q125 81 146 54T209 26T302 45T394 111Q403 121 406 121Q410 121 419 112T429 98T420 82T390 55T344 24T281 -1T205 -11Q126 -11 83 42T39 168ZM373 353Q367 405 305 405Q272 405 244 391T199 357T170 316T154 280T149 261Q149 260 169 260Q282 260 327 284T373 353Z"></path><path stroke-width="0" id="E1-MJMAIN-2212" d="M84 237T84 250T98 270H679Q694 262 694 250T679 230H98Q84 237 84 250Z"></path><path stroke-width="0" id="E1-MJMATHI-73" d="M131 289Q131 321 147 354T203 415T300 442Q362 442 390 415T419 355Q419 323 402 308T364 292Q351 292 340 300T328 326Q328 342 337 354T354 372T367 378Q368 378 368 379Q368 382 361 388T336 399T297 405Q249 405 227 379T204 326Q204 301 223 291T278 274T330 259Q396 230 396 163Q396 135 385 107T352 51T289 7T195 -10Q118 -10 86 19T53 87Q53 126 74 143T118 160Q133 160 146 151T160 120Q160 94 142 76T111 58Q109 57 108 57T107 55Q108 52 115 47T146 34T201 27Q237 27 263 38T301 66T318 97T323 122Q323 150 302 164T254 181T195 196T148 231Q131 256 131 289Z"></path><path stroke-width="0" id="E1-MJMATHI-72" d="M21 287Q22 290 23 295T28 317T38 348T53 381T73 411T99 433T132 442Q161 442 183 430T214 408T225 388Q227 382 228 382T236 389Q284 441 347 441H350Q398 441 422 400Q430 381 430 363Q430 333 417 315T391 292T366 288Q346 288 334 299T322 328Q322 376 378 392Q356 405 342 405Q286 405 239 331Q229 315 224 298T190 165Q156 25 151 16Q138 -11 108 -11Q95 -11 87 -5T76 7T74 17Q74 30 114 189T154 366Q154 405 128 405Q107 405 92 377T68 316T57 280Q55 278 41 278H27Q21 284 21 287Z"></path><path stroke-width="0" id="E1-MJMATHI-76" d="M173 380Q173 405 154 405Q130 405 104 376T61 287Q60 286 59 284T58 281T56 279T53 278T49 278T41 278H27Q21 284 21 287Q21 294 29 316T53 368T97 419T160 441Q202 441 225 417T249 361Q249 344 246 335Q246 329 231 291T200 202T182 113Q182 86 187 69Q200 26 250 26Q287 26 319 60T369 139T398 222T409 277Q409 300 401 317T383 343T365 361T357 383Q357 405 376 424T417 443Q436 443 451 425T467 367Q467 340 455 284T418 159T347 40T241 -11Q177 -11 139 22Q102 54 102 117Q102 148 110 181T151 298Q173 362 173 380Z"></path><path stroke-width="0" id="E1-MJMAIN-3C" d="M694 -11T694 -19T688 -33T678 -40Q671 -40 524 29T234 166L90 235Q83 240 83 250Q83 261 91 266Q664 540 678 540Q681 540 687 534T694 519T687 505Q686 504 417 376L151 250L417 124Q686 -4 687 -5Q694 -11 694 -19Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="matrix(1 0 0 -1 0 0)"><use xlink:href="#E1-MJMATHI-62" x="0" y="0"></use><use xlink:href="#E1-MJMATHI-61" x="429" y="0"></use><use xlink:href="#E1-MJMATHI-6E" x="958" y="0"></use><use xlink:href="#E1-MJMATHI-64" x="1558" y="0"></use><use xlink:href="#E1-MJMATHI-77" x="2081" y="0"></use><use xlink:href="#E1-MJMATHI-69" x="2797" y="0"></use><use xlink:href="#E1-MJMATHI-64" x="3142" y="0"></use><use xlink:href="#E1-MJMATHI-74" x="3665" y="0"></use><g transform="translate(4026,0)"><use xlink:href="#E1-MJMATHI-68" x="0" y="0"></use><g transform="translate(576,-150)"><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-63" x="0" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-6C" x="433" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-69" x="731" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-65" x="1076" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-6E" x="1542" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-74" x="2142" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMAIN-2212" x="2503" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-73" x="3281" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-65" x="3750" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-72" x="4216" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-76" x="4667" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-65" x="5152" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-72" x="5618" y="0"></use></g></g><g transform="translate(9271,0)"><use xlink:href="#E1-MJMAIN-3C"></use><use xlink:href="#E1-MJMAIN-3C" x="778" y="0"></use></g><use xlink:href="#E1-MJMATHI-62" x="11104" y="0"></use><use xlink:href="#E1-MJMATHI-61" x="11533" y="0"></use><use xlink:href="#E1-MJMATHI-6E" x="12062" y="0"></use><use xlink:href="#E1-MJMATHI-64" x="12662" y="0"></use><use xlink:href="#E1-MJMATHI-77" x="13185" y="0"></use><use xlink:href="#E1-MJMATHI-69" x="13901" y="0"></use><use xlink:href="#E1-MJMATHI-64" x="14246" y="0"></use><use xlink:href="#E1-MJMATHI-74" x="14769" y="0"></use><g transform="translate(15130,0)"><use xlink:href="#E1-MJMATHI-68" x="0" y="0"></use><g transform="translate(576,-150)"><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-63" x="0" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-6C" x="433" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-69" x="731" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-65" x="1076" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-6E" x="1542" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-74" x="2142" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMAIN-2212" x="2503" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-63" x="3281" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-61" x="3714" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-63" x="4243" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-68" x="4675" y="0"></use><use transform="scale(0.707)" xlink:href="#E1-MJMATHI-65" x="5252" y="0"></use></g></g></g></svg>

### 应用层协议（Application-Layer Protocols）

应用层协议决定了不同主机上运行的应用程序如何互相通信。

> An application-layer protocol defines how an application’s processes, running on different end systems, pass messages to each other.  

- 应用层协议定义了：
  - 消息的类型（  The types of messages exchanged  ），如请求消息（request messages）、回复消息（response messages）
  - 不同消息类型的语法字段及其定义（  The syntax of the various message types, such as the fields in the message and how the fields are delineated  ）
  - 每个字段的语义（  The semantics of the fields  ）
  - 发送和接受消息的时间和方式（  Rules for determining when and how a process sends messages and responds to messages  ）

- 应用层协议仅仅是应用的一部分

  一个 web 应用的组成：文档格式标准（HTML）、客户端应用（浏览器，如 Firefox 或 Chrome）、服务器应用（web server，如 Apache）、应用层协议（HTTP）

  一个 e-mail 应用的组成：邮件格式标准、mail servers、mail clients（e.g. Outlook）、应用层协议（SMTP）



### Web 应用

1. 一些术语：

   - 网页（web pages）：由对象（objects）组成的文件，对象也是一个文件，可以是 HTML 文件、文本文件，也可以是图片、视频、应用等。绝大多数网页由一个基础 HTML 文件（base HTML file）和若干引用对象（referenced objects）组成

   -  统一资源定位系统（uniform resource locator，URL）：定位对象的标识符，由主机名（hostname）和路径名（pathname）组成，用于寻址某一特定对象

     ![image-20191109235137589](https://s2.ax1x.com/2019/11/11/MlJM1e.png)

2. Web 应用在应用层使用 HTTP 协议

   

### HTTP 协议

1. 

2. 无状态（stateless）：不记录主机信息，比如同一个主机连续两次请求同一个对象，服务器会返回两次，而不是察觉它已经请求过了

3. 持续连接（persistent connection）：一次 TCP 连接处理多个请求（request），默认模式

   非持续连接（non-persistent connection）：一次 TCP 连接处理一个请求（request），但是可以配置多个请求并行（parallel）
   
   🌰：client 向服务器请求一个网页，它包含一个 HTML 文件和 10 张图片，在非持续连接方式下，需要请求 11 次（建立 11 次 TCP 连接），持续方式下只需要 1 次。
   
   - round-trip time（RTT）：数据包（packet）从服务器到客户端**往返**一次的时间（包括传播延迟（packet-propagation delay）、排队延迟（packet-queuing delay）、处理时间（packet-processing delay）<font color="gray">1.4 节</font>）
   
   - 响应时间（response time）：从客户端发起请求到最终收到数据的时间
   
     - non-persistent connection：2 RTT + transmisson time
   
       <img src="https://s2.ax1x.com/2019/11/11/MlJ3nA.md.png" style="zoom:50%;" />
   
4. 两种消息格式：

   - HTTP request message

     - 由 ASCII 码书写

     - 第一行：Request line

       - method：GET / POST / PUT（上传） / DELETE / HEAD（调试）
       - URL
       - Version：HTTP version

     - 接下来若干行：Header lines

       - Host：请求对象所在的主机名（代理服务器需要）
       - User-agent：浏览器类型及版本
       - Connection：指明是否是持续连接
       - ……

     - 空行：额外的回车+换行（carriage return + line feed）

     - entity body：POST 方法需要，用户提交的内容

       ![image-20191110123920991](https://s2.ax1x.com/2019/11/11/MlJQ6H.md.png)

     - 使用 GET 方法提交表单时，不需要填写 Entity body 字段，而是将用户填写的信息放在 request line 的 URL 字段，如：<font style="font-family:courier; font-weight=800">www.somesite.com/animalsearch?monkeys&bananas</font>

   - HTTP response message

     - 第 1 行：status line

       - HTTP version
       - 状态码（status code）

       <img src="https://s2.ax1x.com/2019/11/11/MlJYAP.md.png" style="zoom:50%;" />

       - 状态描述（status phrase）

     - 若干行：header line

       - Connection：指明是否是持续连接
       - Date：服务器发送回执消息的日期时间
       - Server：web server 的类型
       - Last-Modified：对象上次被修改的时间
       - Content-Length：字节长度
       - Content-Type：指明 entity body 字段的对象的文件类型（文件类型由本字段指明，并非文件拓展名）

     - 第 8 行：空行，回车 + 换行

     - entity body：返回的对象文件

     ![image-20191110131328647](https://s2.ax1x.com/2019/11/11/MlJG7t.png)

   - header line 可选非常多，不胜枚举

5. Cookies：用户与服务器的交互

   - 四个组成部分：

     - a cookie header line in HTTP response message
     - a cookie header line in HTTP request message
     - 用户主机上的 cookie 文件，由浏览器管理
     - 后端数据库（back-end database）

   - 🌰：Susan 第一次访问 Amazon

     - 服务器创建一个独一无二的**标识符（identification number）**，并在后端数据库添加了一个以标识符作为索引的表项（entry）
     - 服务器在返回给 Susan 的 response message 中添加了这样一个头部：<font style="font-family:courier;font-weight:bold">Set-cookie: 1678（identification number）</font>
     - Susan 的浏览器收到服务器返回的消息后，向 cookie 文件中追加了服务器的主机名（hostname）以及标识符
     - 之后每次 Susan 向 Amazon 服务器发起请求时，request message 中都带有头部 <font style="font-family:courier;font-weight:bold">Cookie: 1678</font>

     一周后，Susan 再次访问 Amazon，request message 中带有 cookie 头部，服务器根据标识符从数据库中取出有关 Susan 的信息，用于收货地址、商品推荐、购物车合并下单等

   - 隐私问题引起争议

6. Web 缓存 / 代理服务器（Web Cache / proxy server）

   - 代理原服务器（original server）处理用户请求，拥有自己独立的存储空间，存储最近被请求的对象文件
   - 用户浏览器可以被配置为所有请求优先被定位到代理服务器
   - 既是 server 又是 client
   - 由当地 ISP 购置和安装
   - 优点：
     - 大大降低了响应时间（尤其当 \[bandwidth_{client-server} << bandwidth_{client-cache}​\[ 时，通常 client 与 cache 之间的通信都是迅速的）
     - 整体上减小了全网络的交通量，提高了所有应用的性能

   - conditional GET：cache 给服务器发送请求时带上头部  <font style="font-family:courier;font-weight:bold">If-Modified-Since:</font>，如果在这个时间之后对象更新过，服务器返回最新的对象文件；否则不返回任何 entity body 字段，但需要在 status code 字段指明 <font style="font-family:courier;font-weight:bold">304 Not Modified</font>

     条件 GET 用于解决 cache 中的数据和原始服务器数据不同步的问题



### FTP 协议

1. 两条并行的 TCP 连接：

   - 控制连接（control connection）：由 client 发起
   - 数据连接（data connection）：由 server 发起，一次连接只能传输一个文件

   所以 FTP 中控制信息的传输是 out-of-band 的（同理，HTTP 和 SMTP 中控制信息的传输是 in-band 的）

   ![image-20191110151458733](https://s2.ax1x.com/2019/11/11/MlJNh8.png)

2. 建立过程
   - client 发起 TCP 连接（控制连接），端口号 21
   - client 发送用户名和密码，并发送改变目录的命令
   - server 发起 TCP 连接（数据连接），端口号 20
3. FTP 服务器是有状态（state）的，用户账户和控制连接是绑定的，服务器必须可以追踪用户当前访问的目录（这也限制了 FTP 服务器可以同时服务的会话（session）数量）
4. 报文格式：
   - command（client→server）：4 位大写 ASCII 字母
   - reply（server→client）：3 位数字，类似于 status code
5. 与 HTTP 比较：
   - 底层都是 TCP 协议
   - 最大的区别是 FTP 的数据连接和控制连接是分开的



### SMTP 协议

1. 电子邮件系统的组成：
   -  User Agent：允许用户查看、回复、转发、保存、编辑新邮件，如 OutLook 等
   - mail server：核心，保存用户收到的邮件 / 发送邮件
   - Simple Mail Transfer Protocol（SMTP）：决定了发件人发送的邮件如何被送给收件人

2. 电子邮件发送和收取的过程：

   🌰：Alice 想要发一封 email 给 Bob

   - Alice 用她的 user agent 编辑好了一封邮件，点击发送
   - Alice 的 user agent 把这封邮件发送到她的 mail server 上，在那里，这封邮件进入发件队列（outgoing message queue）
   - 这封邮件被发送到 Bob 的 mail server 上（发送可能失败，Alice 的 mail server 将这封邮件放入消息队列（message queue）中，隔一段时间再尝试发送；如果发送还是多次失败，这封邮件将从消息队列中移除，并且邮件服务器将会告知 Alice）
   - Bob 的 mail server 收到邮件并保存在 mailbox 中
   - 当 Bob 查看他的邮件时，这封邮件将被从 mailbox 中取出

   ![image-20191110212136691](https://s2.ax1x.com/2019/11/11/MlJa9S.png)

3. SMTP 协议概述

   - 过程
     - SMTP 的客户端（运行在发件服务器主机上）向 SMTP 的服务器端（运行在收件服务器主机上）发起 TCP 连接，端口号 25
     - 连接建立，SMTP 握手（应用层），握手过程中 client 需要确认发件和收件的地址
     - 客户端把邮件发送给服务器端
     - 
   - 不需要中间服务器，发件和收件服务器直接建立 TCP 连接通信
   - persistent TCP connection

4. HTTP 与 SMTP 比较

   |                         SMTP                         |                HTTP                 |
   | :--------------------------------------------------: | :---------------------------------: |
   |         push protocol，TCP 连接由发件方发起          | pull protocol，TCP 连接由收件方发起 |
   | 报文必须是 7 位 ASCII 码格式，图片等必须编码后再传输 |              没有限制               |
   |               每个报文可以传输多个对象               | 每个对象都需要由独立的 TCP 连接传输 |

5. mail access protocols（收件协议）

   一般来说，user agent 运行在用户主机上，但是 mailbox 存储在常开的服务器上，这个服务器一般由当地 ISP 架设。mail access protocol 决定了邮件如何从收件人的 mail server 传输到收件人的主机上。

   - Post Office Protocol version 3，POP3
   - International Mail Access Protocol，IMAP



### DNS 协议

1. 标识主机的两种方法：
   - 主机名（hostname）：方便记忆，数字和字母的组合，对主机地址信息没有说明，并且对路由不友好
   - IP 地址（IP address）：指明主机在因特网上的地址，4 字节，长度固定，层次化

2. DNS 协议

   用户偏好主机名，路由偏好 IP 地址，所以需要一种目录服务将主机名解析成 IP 地址，DNS 就是为此而生的

   - DNS（Domain Name System）是：
     - DNS 是一个分布式数据库（distributed database）运行在一系列 DNS 服务器上
     - 允许不同主机查询数据库的应用层协议

   - 域名解析过程：

     🌰：用户的主机请求 URL <font style="font-family:courier;font-weight:bold">www.someschool.edu/index.html</font>

     - DNS 的客户端运行在用户主机上
     - 浏览器提取 URL 中的 hostname 部分 <font style="font-family:courier;font-weight:bold">www.someschool.edu</font> 并将其发送给 DNS 客户端
     - DNS 客户端向服务器发送请求，询问该主机名对应的 IP 地址
     - DNS 客户端收到服务器应答，得到 IP 地址并告知浏览器
     - 浏览器发起 TCP 连接
     - ……

   - DNS 应用提供的其他服务：
     
     - host aliasing
   - 所有 DNS 请求和应答都通过 UDP 数据包传输，端口号 53



## Chapter 3 Transport Layer

### 概览

1. 传输层协议为不同主机上的应用**进程**提供逻辑通信（logical communication），网络层协议为不同**主机**提供逻辑通信

   逻辑通信：在应用进程的角度来看，两个主机是直接相连的（但事实上，两个主机在地理上可能相距甚远，并且通过很多路由和不同的链接方式才连接到一起）

2. 在一个终端系统内，传输层协议将应用进程的消息传递到网络的边缘（网络层），但是并不关注消息在网络核心中是如何传输的

   > Within an end system, a transport protocol moves messages from application processes to the network edge (that is, the network layer) and vice versa, but it doesn’t have any say about how the messages are moved within the network core.

3. 传输层协议所能提供的服务受制于低层的网络层协议（延时和带宽）

   >the services that a transport protocol can provide are often constrained by the service model of the underlying network-layer protocol.

   但是，当底层的网络协议是不可靠的时候，传输层协议仍可以提供可靠的数据传输（加密保证报文不在传输过程中被读取读取）

   >a transport protocol can offer reliable data transfer service to an application even when the underlying network protocol is unreliable 

4. 传输层的数据包（packet）叫做 **segment**。TCP 的数据包一般也叫做 segment，但是 UDP 的数据包一般叫做 datagram（网络层数据包也叫 datagram）。
5. 传输层协议的最主要任务：**将端到端的传输服务扩展为进程到进程的传输服务**（Extending host-to-host delivery to process-to-process delivery），即 multiplexing 和 demultiplexing



### 复用 & 分用（multiplexing & demultiplexing）

1. 复用（multiplexing）

   - 发生于源主机，从源主机上不同的 socket 处收集数据块，并为它们加上不同的头部，封装成 segments，发送到网络层

   - 要求：

     - socket 有唯一标识符

     - 每个 segment 包含特殊字段表示目的 socket，即源端口号（source port number）字段和目的端口号（destination port number）字段

       端口号是 16 位数字（0\~65535），0\~1023 是周知端口（well known port numbers）

   ![image-20191111102719060](https://s2.ax1x.com/2019/11/11/MlJwcQ.png)

2. 分用（demultiplexing）

   发生于目的主机，从目的主机网处收集 segments，解析其中的字段，并将它们转发到对应的 socket 处

3. TCP 与 UDP 的区别
   - TCP 的 socket 标识符是 4 个元组：源 IP 地址、源端口号、目的 IP 地址、目的端口号
   - UDP 的 socket 标识符是 2 个元组：目的 IP 地址、目的端口号



### 可靠数据传输（reliable data transfer）

1. idt 1.0

2. idt 2.0

   - ARQ（automatic repeat request） 协议

   - 追加内容：
     - 错误检测（error detection）
     - 收件人回执（receiver feedback）：positive acknowledgement（ACK） / negative acknowledgement（NAK）
     - 重新传输（retransmission）：收件方收到错误数据时可以发送回执 NAK，要求发送方重新发送
   - 由于在这类协议中，仅当 sender 确认 receiver 已经正确收到当前数据包才会发送下一个，所以这类协议也叫做 **stop-and-wait** 协议

   有限状态机（FSM）表示：![image 20191111205826706](https://s2.ax1x.com/2019/11/11/MlJrBn.png) 

3. idt 2.1 （improved version of idt 2.0）

   - 解决问题：idt 2.0 中 ACK / NAK 数据包出错时无法校验
   - 改进方法：在数据包和回执中带上序列号

   FSM 表示：

    ![image 20191111212512209](https://s2.ax1x.com/2019/11/11/MlJ6A0.png)  

   ![](https://s2.ax1x.com/2019/11/11/MlJcNV.md.png)

4. idt 2.2

   - 改进：移除了 NAK 数据包，ACK 包发送上一次检测正确的序列号，即如果两次连续 ACK 序列号一致（duplicate ACKs）表明发生错误

   FSM 表示：

   ![image-20191111213731945](https://s2.ax1x.com/2019/11/11/MlJghT.png)

   ![image-20191111213903035](https://s2.ax1x.com/2019/11/11/MlJR9U.png)

5. idt 3.0

   - 基于假设：底层通道会丢包（lose packet）
   - 解决问题：如何检测丢包？如何解决？

6. 管道（Pipelining）：

   ![image-20191111214926283](https://s2.ax1x.com/2019/11/11/MlJW3F.png)

   ![](https://s2.ax1x.com/2019/11/11/MlU8xS.md.png)

   - 基于问题：传统的 idt 方式使用 stop-and-wait 方式，导致 sender 的带宽利用率非常低（大部分时间都用于 propagate 了）

   - 改进方法：sender 允许一次发送多个包，不用等待回执
   
   - 带来的改变：
   
     - 序列号范围增加
   
     - 协议双方都需要增加缓冲区，sender 需要缓存已经送出但没收到回执的数据包
     
     - 新的错误检测和恢复（error detect and restore）方法
     - **Go-Back-N**（GBN）	——	“fill the pipeline
       
         - 允许 sender 一次发送多个数据包，但是管道中最多同时存在 N 个未收到回执的数据包（包括已发出的和未发出的）
         
         - N 指窗口大小（window size），GBN 协议也叫滑动窗口协议（sliding window protocol）
         
         - 收到的数据包顺序错误也会被拒绝，返回的 ACK 包含最后收到的顺序正确的序列号
         
         - 使用 累积确认（cumulative acknowledgement），即确认了第 $n$ 个，表明已经确认了 $n$ 以下的所有数据包
         
         - 对于丢包问题，使用超时事件（timeout event）
         
         - sender 需要缓存：window 的上下界、<font style="font-family:courier;font-weight:bold">nextseqnum</font> 在窗口中的位置
         
         - receiver 需要缓存：下一个顺序数据包的队列号
         
         - 为什么施加窗口大小 N 的限制？流量控制和拥塞控制
         
             ![page0](https://s2.ax1x.com/2019/11/11/MlUcqJ.png) 
         
         - FSM 表示：
         
            ![page0](https://s2.ax1x.com/2019/11/11/MlrEFJ.png)
         
            ![page0](https://s2.ax1x.com/2019/11/11/Mlr8FH.png)  
         
       - **selective repeat**（SR） 
       
         - 针对问题：GBN 的性能问题，当单个数据包出现错误时，可能导致重传多次
         - 对传输正确的数据包**分别**进行确认
         - 不在乎数据包是否按顺序到达，只要正确都给予确认回执（ACK），乱序的数据包将先被缓存
       
         - receiver 将数据发给上层协议时按批（batch）进行
       
         - sender 和 receiver 的窗口状态未必一致
       
         - 窗口大小必须 $\leq$ 序列号范围长度的一半
       
            ![page0](https://s2.ax1x.com/2019/11/11/MlcIDx.png) 
       
     - 总结
     
     <img src="C:%5CUsers%5Caqyjz%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191112000035950.png" alt="image-20191112000035950" style="zoom:120%;" />



### UDP 协议（User Datagram Protocol）

1. 提供的服务
   - process-to-process delivery
   - error checking
2. 特点：
   - 无连接，无握手过程
   - 
3. UDP 数据报（datagram）格式
   - 头部（header）：8 bytes



### TCP 协议（Transmission Control Protocol）

1. TCP 的特点：

   - 依赖很多底层的

   - 双向（full-duplex / bidirectional）通信：连接建立后，从 client 到 server 的数据和从 server 到 client 的数据可以同时传送
   - 点到点（point-to-point）：连接建立在两个独立主机之间
   - TCP 存在于终端系统内，与底层的网络无关
   - 连接建立之初需要建立并保存一系列 TCP 状态变量

2. TCP 连接建立过程：

   - client 进程告知 client 主机的传输层它希望与 server 主机的 server 进程建立连接

   - client 主机的传输层向 server 主机的传输层发起 TCP 连接

   - **三次握手（three-way handshake）**

     - 三次握手过程中分别为发送方和收取方预留出 send buffer 和 receive buffer

   - 数据传输

     - maximum segment size（MSS）：segment 数据段的最大长度，即最大**应用层数据**长度（不包含头部）

       超过 MSS 的数据需要分包发送

     - maximum transmission unit（MTU）

3. TCP 报文段（segment）格式

   <img src="https://s2.ax1x.com/2019/11/11/MlJDns.png" alt="image-20191111114807225" style="zoom:67%;" />

   - 头部（header）：20 bytes

     - 源端口号和目标端口号（16 bits * 2）：用于复用、分用

     - 序列号（32 bits）、确认号（32 bits）：用于可靠数据传输

       - **序列号（sequence number）**是报文段中第一个字节的字节流序号（TCP 将报文看作是非结构化的字节流）

         > The sequence number for a segment is therefore the byte-stream number of the first byte in the segment.

         🌰：数据流包含 5000 个字节，MSS 长度为 100，那么这条数据需要分为 50 个 TCP 报文段传输，第一个 segment 的序列号为 0，第二个为 1，……，以此类推

       - **确认号（acknowledgement number）**是指双向通信中一个主机期待收到的另一个主机发送过来下一条报文的序列号

         🌰：主机 A 与主机 B 通信，现在主机 A 已经收到了序列号为 0~53 的 segment，并期待主机 B 从 54 开始继续发送，那么主机 A 可以给主机 B 发送 segment，其中确认号为 54

     - 接收窗口（16 bits）：用于流量控制（flow control）

     - 头部长度（4 bits）

     - 标志字段（6 bits）

       - ACK：用于连接确认
       - RST / SYN / FIN：用于连接建立和断开
       - PSH：接收方需要立即把数据传送给高层
       - URG：指明存在紧急（urgent）信息，地址由 urgent data pointer 字段（16 bits）说明

     - 选项字段（可选，变长）：发送方和接收方需要商讨 MSS 等