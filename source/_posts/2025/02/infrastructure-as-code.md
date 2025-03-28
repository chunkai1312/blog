---
title: 《基礎架構即程式碼》：從基礎架構管理到軟體工程思維
tags: [Infrastructure, IaC, DevOps]
categories: 閱讀筆記
date: 2025-02-26 00:59:46
index_img: /2025/02/26/infrastructure-as-code/cover.png
---

![](/2025/02/26/infrastructure-as-code/cover.png)

這本書在我的書架上擺了好幾年，最近終於靜下心來完整讀完。回顧這些年的開發經歷，從手動配置環境、撰寫部署腳本，到逐步引入自動化，IaC（Infrastructure as Code）已經成為現代軟體開發的核心之一。而這本書則提供了一種更系統化的思考方式，讓我重新審視現有的基礎架構管理模式。  

<!-- more -->

## 從腳本驅動到系統化管理：IaC 的轉變

在小型開發團隊中，工程師往往身兼數職，除了後端開發，還要負責 CI/CD pipeline、監控、基礎設施維運等工作。初期，這些任務或許只需幾個 Ansible playbook 或 Terraform module 就能解決，但隨著系統複雜度提升，技術債也隨之累積，例如：  
- **環境不一致**，導致部署問題層出不窮  
- **手動操作風險**，缺乏標準化流程，稍有不慎便可能影響系統穩定性  
- **變更難以追蹤**，基礎架構缺乏版本控制與審查機制  

讀完本書讓我意識到，IaC 不只是寫幾個 Terraform 或 CloudFormation 檔案，而是一種軟體工程化的管理方式，涵蓋版本控制、測試、審核流程，以及與應用開發一致的敏捷迭代思維。  

## 以軟體工程思維打造穩定可維護的基礎架構

作者強調，IaC 的核心在於將基礎架構視為軟體，而非一組靜態的伺服器配置。這種觀念與現代 DevOps 團隊的最佳實踐相符，例如：  
- **GitOps** —— 透過 Git 管理基礎架構狀態，確保變更可追蹤、可回滾  
- **Immutable Infrastructure** —— 降低手動修改帶來的不確定性，透過完整替換來確保環境一致性  
- **Policy-as-Code** —— 透過自動化測試，確保基礎架構變更符合既定標準  

這些方法部分已經應用在目前開發的日常工作中，但仍有許多優化空間，特別是在環境一致性與變更管理方面。  

## 小型團隊的挑戰與對大型 DevOps 團隊的想像

在小型開發團隊中，DevOps 角色通常是「誰會誰做」，不像大公司有專職 SRE 或 Platform Engineering 團隊來維護基礎設施。這種靈活性雖然帶來了一定的彈性，但也讓流程標準化變得困難。  

此外，小型團隊的 IaC 實踐往往是從解決眼前問題出發，例如用 Terraform 或 Ansible 來管理雲端資源，或是用 Kubernetes YAML 來定義應用部署。但讀完這本書後，我開始思考，在大型 DevOps 團隊中，IaC 的實踐應該更加成熟，例如：  
- **如何在多人協作下維護大規模的 IaC repository？**  
- **如何設計可複用、可擴展的 IaC 模組？**  
- **如何透過策略與治理機制，確保基礎架構變更符合合規性要求？**  

這些問題或許在小型團隊中尚未構成迫切需求，但如果未來有機會參與更大規模的 DevOps 團隊，這些應該是值得深入學習的方向。  

## **結語：基礎架構應該像軟體一樣被管理**  

《基礎架構即程式碼》不僅是一本技術指南，更是一種開發哲學，提醒我們基礎架構應該像軟體一樣被管理，而不是一堆難以追蹤的手動操作。在小型開發團隊中，這樣的轉變雖然需要時間，但長遠來看，它能幫助我們降低風險、提升可維護性。  

而在更大規模的 DevOps 團隊中，這些原則又會如何落實？這或許是我未來想要進一步探索的方向。
