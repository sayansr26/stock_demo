## StockBrief - AI-Powered Stock News in 50 Words

**Version:** 5.0 (Unified Backend Edition)

**Date:** November 2, 2025

**Status:** Approved for Development

**Target Launch:** Week 4 (Month 1 MVP)

---

## 📑 Table of Contents

1. [Executive Summary](#executive-summary)
2. [System Architecture](#system-architecture)
3. [Project Structure](#project-structure)
4. [Technical Specifications](#technical-specifications)
5. [NestJS Backend Implementation](#nestjs-backend-implementation)
6. [Admin Panel Integration](#admin-panel-integration)
7. [Development Phases](#development-phases)
8. [API Specifications](#api-specifications)
9. [Success Metrics](#success-metrics)
10. [Quick Reference](#quick-reference)

---

## 1. Executive Summary

### 1.1 Product Name

**StockBrief** - Your Daily Dose of Stock News

**Tagline:** "50 Words. Your Stocks. Stay Ahead."

### 1.2 Solution

A mobile-first news aggregation app with AI-powered 50-word summaries. Features a **single NestJS backend** serving both API endpoints for mobile and an integrated admin panel with server-side rendering.

### 1.3 Key Architecture Decisions

1. **Single Backend:** NestJS serves both API and admin panel - no separate projects
2. **Server-Side Rendering:** Admin panel uses Handlebars templates (no React needed)
3. **Session-Based Auth:** Admin uses sessions, API uses JWT
4. **Local LLM:** Ollama for privacy and zero API costs
5. **Clean Architecture:** Complete separation of concerns

### 1.4 Target Markets

- **Primary Indices:** NSE, BSE, NYSE, NASDAQ, FOREX

---

## 2. System Architecture

### 2.1 Simplified Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     FLUTTER MOBILE APP                       │
│                         (Latest v3.27+)                      │
├───────────────────────────────────────────────────────────────┤
│  Clean Architecture: Presentation → Domain → Data            │
│  State Management: Riverpod 2.6+                            │
│  Network: Dio 5.7+ with Interceptors                        │
└──────────────────────────┬──────────────────────────────────┘
                           │ HTTPS/WebSocket
                           ▼
┌───────────────────────────────────────────────────────────────┐
│              NESTJS UNIFIED BACKEND (v10.4.8+)               │
├───────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌─────────────────────┐     ┌─────────────────────┐        │
│  │   API Routes (/api) │     │  Admin Panel (/admin)│        │
│  │   - JWT Auth        │     │  - Session Auth      │        │
│  │   - REST Endpoints  │     │  - Server-Side Views │        │
│  │   - WebSocket       │     │  - Handlebars/EJS    │        │
│  └─────────────────────┘     └─────────────────────┘        │
│                                                               │
│  ┌──────────────────────────────────────────────────┐        │
│  │           Shared Business Logic Layer            │        │
│  │  Services, Repositories, Entities, Domain Logic  │        │
│  └──────────────────────────────────────────────────┘        │
│                                                               │
└───────────┬──────────────┬─────────────┬────────────────────┘
            │              │             │
    ┌───────▼──────┐ ┌────▼─────┐ ┌────▼──────────┐
    │ PostgreSQL   │ │  Redis   │ │   Ollama      │
    │   (v16)      │ │  (v7.4)  │ │  (Local LLM)  │
    └──────────────┘ └──────────┘ └───────────────┘
```

---

## 3. Project Structure

### 3.1 Complete Repository Structure (Detailed High-Level)

```
stockbrief/
├── mobile_app/                        # Flutter Mobile Application
│   ├── android/                       # Android-specific configuration
│   ├── ios/                          # iOS-specific configuration
│   ├── lib/
│   │   ├── core/                     # Core Layer - Foundation
│   │   │   ├── constants/
│   │   │   │   ├── api_endpoints.dart
│   │   │   │   ├── app_colors.dart
│   │   │   │   ├── app_dimensions.dart
│   │   │   │   ├── app_strings.dart
│   │   │   │   └── app_themes.dart
│   │   │   ├── errors/
│   │   │   │   ├── exceptions.dart
│   │   │   │   └── failures.dart
│   │   │   ├── network/
│   │   │   │   ├── api_client.dart
│   │   │   │   ├── api_interceptors.dart
│   │   │   │   ├── api_response.dart
│   │   │   │   └── network_info.dart
│   │   │   ├── utils/
│   │   │   │   ├── date_formatter.dart
│   │   │   │   ├── validators.dart
│   │   │   │   ├── extensions.dart
│   │   │   │   └── logger.dart
│   │   │   └── di/
│   │   │       ├── injection_container.dart
│   │   │       └── modules/
│   │   │           ├── network_module.dart
│   │   │           └── storage_module.dart
│   │   │
│   │   ├── data/                     # Data Layer - Implementation
│   │   │   ├── datasources/
│   │   │   │   ├── local/
│   │   │   │   │   ├── auth/
│   │   │   │   │   │   └── auth_local_datasource.dart
│   │   │   │   │   ├── cache/
│   │   │   │   │   │   └── cache_datasource.dart
│   │   │   │   │   └── preferences/
│   │   │   │   │       └── preferences_datasource.dart
│   │   │   │   └── remote/
│   │   │   │       ├── auth/
│   │   │   │       │   └── auth_remote_datasource.dart
│   │   │   │       ├── feed/
│   │   │   │       │   └── feed_remote_datasource.dart
│   │   │   │       ├── stocks/
│   │   │   │       │   └── stocks_remote_datasource.dart
│   │   │   │       └── articles/
│   │   │   │           └── articles_remote_datasource.dart
│   │   │   ├── models/
│   │   │   │   ├── user/
│   │   │   │   │   ├── user_model.dart
│   │   │   │   │   └── user_model.g.dart
│   │   │   │   ├── article/
│   │   │   │   │   ├── article_model.dart
│   │   │   │   │   └── article_model.g.dart
│   │   │   │   ├── stock/
│   │   │   │   │   ├── stock_model.dart
│   │   │   │   │   └── stock_model.g.dart
│   │   │   │   └── index/
│   │   │   │       ├── index_model.dart
│   │   │   │       └── index_model.g.dart
│   │   │   ├── repositories/
│   │   │   │   ├── auth_repository_impl.dart
│   │   │   │   ├── feed_repository_impl.dart
│   │   │   │   ├── stocks_repository_impl.dart
│   │   │   │   └── articles_repository_impl.dart
│   │   │   └── mappers/
│   │   │       ├── entity_to_model_mapper.dart
│   │   │       └── model_to_entity_mapper.dart
│   │   │
│   │   ├── domain/                   # Domain Layer - Business Logic
│   │   │   ├── entities/
│   │   │   │   ├── user/
│   │   │   │   │   └── user.dart
│   │   │   │   ├── article/
│   │   │   │   │   ├── article.dart
│   │   │   │   │   └── sentiment.dart
│   │   │   │   ├── stock/
│   │   │   │   │   └── stock.dart
│   │   │   │   └── index/
│   │   │   │       └── market_index.dart
│   │   │   ├── repositories/
│   │   │   │   ├── auth_repository.dart
│   │   │   │   ├── feed_repository.dart
│   │   │   │   ├── stocks_repository.dart
│   │   │   │   └── articles_repository.dart
│   │   │   ├── usecases/
│   │   │   │   ├── auth/
│   │   │   │   │   ├── login_usecase.dart
│   │   │   │   │   ├── register_usecase.dart
│   │   │   │   │   ├── logout_usecase.dart
│   │   │   │   │   └── refresh_token_usecase.dart
│   │   │   │   ├── feed/
│   │   │   │   │   ├── get_feed_usecase.dart
│   │   │   │   │   ├── refresh_feed_usecase.dart
│   │   │   │   │   └── filter_feed_usecase.dart
│   │   │   │   ├── stocks/
│   │   │   │   │   ├── get_stocks_usecase.dart
│   │   │   │   │   ├── search_stocks_usecase.dart
│   │   │   │   │   └── save_stocks_usecase.dart
│   │   │   │   └── articles/
│   │   │   │       ├── save_article_usecase.dart
│   │   │   │       ├── unsave_article_usecase.dart
│   │   │   │       └── get_saved_articles_usecase.dart
│   │   │   └── value_objects/
│   │   │       ├── email_address.dart
│   │   │       ├── password.dart
│   │   │       └── stock_symbol.dart
│   │   │
│   │   └── presentation/             # Presentation Layer - UI
│   │       ├── providers/
│   │       │   ├── auth/
│   │       │   │   └── auth_provider.dart
│   │       │   ├── feed/
│   │       │   │   └── feed_provider.dart
│   │       │   ├── stocks/
│   │       │   │   └── stocks_provider.dart
│   │       │   └── theme/
│   │       │       └── theme_provider.dart
│   │       ├── screens/
│   │       │   ├── auth/
│   │       │   │   ├── login/
│   │       │   │   │   ├── login_screen.dart
│   │       │   │   │   ├── login_viewmodel.dart
│   │       │   │   │   └── widgets/
│   │       │   │   │       ├── login_form.dart
│   │       │   │   │       └── social_login_buttons.dart
│   │       │   │   ├── register/
│   │       │   │   │   ├── register_screen.dart
│   │       │   │   │   ├── register_viewmodel.dart
│   │       │   │   │   └── widgets/
│   │       │   │   └── splash/
│   │       │   │       └── splash_screen.dart
│   │       │   ├── onboarding/
│   │       │   │   ├── index_selection/
│   │       │   │   │   ├── index_selection_screen.dart
│   │       │   │   │   ├── index_selection_viewmodel.dart
│   │       │   │   │   └── widgets/
│   │       │   │   │       └── index_card.dart
│   │       │   │   └── stock_selection/
│   │       │   │       ├── stock_selection_screen.dart
│   │       │   │       ├── stock_selection_viewmodel.dart
│   │       │   │       └── widgets/
│   │       │   │           ├── stock_search_bar.dart
│   │       │   │           └── stock_list_item.dart
│   │       │   ├── feed/
│   │       │   │   ├── feed_screen.dart
│   │       │   │   ├── feed_viewmodel.dart
│   │       │   │   └── widgets/
│   │       │   │       ├── article_card.dart
│   │       │   │       ├── swipe_detector.dart
│   │       │   │       └── sentiment_badge.dart
│   │       │   ├── article_detail/
│   │       │   │   └── article_detail_screen.dart
│   │       │   ├── saved/
│   │       │   │   ├── saved_articles_screen.dart
│   │       │   │   └── saved_articles_viewmodel.dart
│   │       │   ├── profile/
│   │       │   │   ├── profile_screen.dart
│   │       │   │   └── profile_viewmodel.dart
│   │       │   └── settings/
│   │       │       ├── settings_screen.dart
│   │       │       └── settings_viewmodel.dart
│   │       ├── widgets/                # Shared Widgets
│   │       │   ├── buttons/
│   │       │   │   ├── primary_button.dart
│   │       │   │   └── secondary_button.dart
│   │       │   ├── inputs/
│   │       │   │   ├── text_field.dart
│   │       │   │   └── search_field.dart
│   │       │   ├── cards/
│   │       │   │   └── base_card.dart
│   │       │   └── loaders/
│   │       │       ├── shimmer_loader.dart
│   │       │       └── circular_loader.dart
│   │       └── routes/
│   │           ├── app_router.dart
│   │           ├── route_guards.dart
│   │           └── route_names.dart
│   ├── test/
│   │   ├── unit/
│   │   ├── widget/
│   │   └── integration/
│   ├── assets/
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   └── pubspec.yaml
│
├── backend/                          # NestJS Backend + Admin Panel
│   ├── src/
│   │   ├── main.ts
│   │   ├── app.module.ts
│   │   │
│   │   ├── common/                  # Shared/Cross-Cutting Concerns
│   │   │   ├── decorators/
│   │   │   │   ├── api-response.decorator.ts
│   │   │   │   ├── current-user.decorator.ts
│   │   │   │   ├── roles.decorator.ts
│   │   │   │   └── transform.decorator.ts
│   │   │   ├── filters/
│   │   │   │   ├── http-exception.filter.ts
│   │   │   │   ├── validation-exception.filter.ts
│   │   │   │   └── all-exceptions.filter.ts
│   │   │   ├── guards/
│   │   │   │   ├── jwt-auth.guard.ts
│   │   │   │   ├── admin-auth.guard.ts
│   │   │   │   ├── roles.guard.ts
│   │   │   │   └── throttle.guard.ts
│   │   │   ├── interceptors/
│   │   │   │   ├── logging.interceptor.ts
│   │   │   │   ├── transform.interceptor.ts
│   │   │   │   ├── cache.interceptor.ts
│   │   │   │   └── timeout.interceptor.ts
│   │   │   ├── pipes/
│   │   │   │   ├── validation.pipe.ts
│   │   │   │   ├── parse-uuid.pipe.ts
│   │   │   │   └── sanitization.pipe.ts
│   │   │   ├── middleware/
│   │   │   │   ├── logger.middleware.ts
│   │   │   │   ├── session.middleware.ts
│   │   │   │   └── request-id.middleware.ts
│   │   │   └── interfaces/
│   │   │       ├── pagination.interface.ts
│   │   │       ├── response.interface.ts
│   │   │       └── request.interface.ts
│   │   │
│   │   ├── config/                  # Configuration Module
│   │   │   ├── config.module.ts
│   │   │   ├── configurations/
│   │   │   │   ├── app.config.ts
│   │   │   │   ├── database.config.ts
│   │   │   │   ├── jwt.config.ts
│   │   │   │   ├── redis.config.ts
│   │   │   │   ├── session.config.ts
│   │   │   │   ├── ollama.config.ts
│   │   │   │   └── swagger.config.ts
│   │   │   └── validation/
│   │   │       └── env.validation.ts
│   │   │
│   │   ├── database/                # Database Module
│   │   │   ├── database.module.ts
│   │   │   ├── database.providers.ts
│   │   │   ├── entities/
│   │   │   │   └── base.entity.ts
│   │   │   ├── migrations/
│   │   │   ├── seeds/
│   │   │   └── repositories/
│   │   │       └── base.repository.ts
│   │   │
│   │   ├── modules/                 # Feature Modules
│   │   │   │
│   │   │   ├── auth/                # Authentication Module
│   │   │   │   ├── auth.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   ├── auth.controller.ts
│   │   │   │   │   └── auth.controller.spec.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── auth.service.ts
│   │   │   │   │   ├── jwt.service.ts
│   │   │   │   │   ├── password.service.ts
│   │   │   │   │   └── oauth.service.ts
│   │   │   │   ├── strategies/
│   │   │   │   │   ├── jwt.strategy.ts
│   │   │   │   │   ├── local.strategy.ts
│   │   │   │   │   └── google.strategy.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── login.dto.ts
│   │   │   │   │   ├── register.dto.ts
│   │   │   │   │   ├── refresh-token.dto.ts
│   │   │   │   │   └── auth-response.dto.ts
│   │   │   │   ├── entities/
│   │   │   │   │   └── refresh-token.entity.ts
│   │   │   │   └── repositories/
│   │   │   │       └── refresh-token.repository.ts
│   │   │   │
│   │   │   ├── admin/               # Admin Module
│   │   │   │   ├── admin.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   ├── admin-auth.controller.ts
│   │   │   │   │   ├── admin-dashboard.controller.ts
│   │   │   │   │   ├── admin-articles.controller.ts
│   │   │   │   │   ├── admin-users.controller.ts
│   │   │   │   │   ├── admin-llm.controller.ts
│   │   │   │   │   └── admin-sources.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── admin-auth.service.ts
│   │   │   │   │   ├── admin-dashboard.service.ts
│   │   │   │   │   └── admin-stats.service.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── admin-login.dto.ts
│   │   │   │   │   ├── dashboard-stats.dto.ts
│   │   │   │   │   └── bulk-action.dto.ts
│   │   │   │   └── entities/
│   │   │   │       └── admin-user.entity.ts
│   │   │   │
│   │   │   ├── users/               # Users Module
│   │   │   │   ├── users.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   └── users.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── users.service.ts
│   │   │   │   │   └── user-preferences.service.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── create-user.dto.ts
│   │   │   │   │   ├── update-user.dto.ts
│   │   │   │   │   └── user-response.dto.ts
│   │   │   │   ├── entities/
│   │   │   │   │   ├── user.entity.ts
│   │   │   │   │   └── user-preferences.entity.ts
│   │   │   │   └── repositories/
│   │   │   │       └── users.repository.ts
│   │   │   │
│   │   │   ├── indices/             # Market Indices Module
│   │   │   │   ├── indices.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   └── indices.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   └── indices.service.ts
│   │   │   │   ├── dto/
│   │   │   │   │   └── index-response.dto.ts
│   │   │   │   ├── entities/
│   │   │   │   │   ├── index.entity.ts
│   │   │   │   │   └── user-index.entity.ts
│   │   │   │   └── repositories/
│   │   │   │       └── indices.repository.ts
│   │   │   │
│   │   │   ├── stocks/              # Stocks Module
│   │   │   │   ├── stocks.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   └── stocks.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── stocks.service.ts
│   │   │   │   │   └── stock-search.service.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── stock-search.dto.ts
│   │   │   │   │   └── stock-response.dto.ts
│   │   │   │   ├── entities/
│   │   │   │   │   ├── stock.entity.ts
│   │   │   │   │   └── user-stock.entity.ts
│   │   │   │   └── repositories/
│   │   │   │       └── stocks.repository.ts
│   │   │   │
│   │   │   ├── articles/            # Articles Module
│   │   │   │   ├── articles.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   ├── articles.controller.ts
│   │   │   │   │   └── feed.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── articles.service.ts
│   │   │   │   │   ├── feed.service.ts
│   │   │   │   │   └── article-processor.service.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── article-response.dto.ts
│   │   │   │   │   ├── feed-query.dto.ts
│   │   │   │   │   └── create-article.dto.ts
│   │   │   │   ├── entities/
│   │   │   │   │   ├── article.entity.ts
│   │   │   │   │   ├── saved-article.entity.ts
│   │   │   │   │   └── article-interaction.entity.ts
│   │   │   │   └── repositories/
│   │   │   │       └── articles.repository.ts
│   │   │   │
│   │   │   ├── llm/                 # LLM Integration Module
│   │   │   │   ├── llm.module.ts
│   │   │   │   ├── controllers/
│   │   │   │   │   └── llm-health.controller.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── ollama.service.ts
│   │   │   │   │   ├── summarizer.service.ts
│   │   │   │   │   ├── prompt-builder.service.ts
│   │   │   │   │   └── model-manager.service.ts
│   │   │   │   ├── adapters/
│   │   │   │   │   ├── ollama.adapter.ts
│   │   │   │   │   └── llm-provider.adapter.ts
│   │   │   │   ├── dto/
│   │   │   │   │   ├── summary-request.dto.ts
│   │   │   │   │   ├── summary-response.dto.ts
│   │   │   │   │   └── model-stats.dto.ts
│   │   │   │   └── interfaces/
│   │   │   │       ├── llm-provider.interface.ts
│   │   │   │       └── summary-result.interface.ts
│   │   │   │
│   │   │   ├── news/                # News Aggregation Module
│   │   │   │   ├── news.module.ts
│   │   │   │   ├── services/
│   │   │   │   │   ├── news-aggregator.service.ts
│   │   │   │   │   ├── news-processor.service.ts
│   │   │   │   │   └── source-manager.service.ts
│   │   │   │   ├── adapters/
│   │   │   │   │   ├── reuters.adapter.ts
│   │   │   │   │   ├── bloomberg.adapter.ts
│   │   │   │   │   ├── economic-times.adapter.ts
│   │   │   │   │   └── base-news.adapter.ts
│   │   │   │   ├── jobs/
│   │   │   │   │   ├── news-fetch.processor.ts
│   │   │   │   │   ├── summary.processor.ts
│   │   │   │   │   └── queue.config.ts
│   │   │   │   ├── entities/
│   │   │   │   │   └── news-source.entity.ts
│   │   │   │   └── interfaces/
│   │   │   │       └── news-source.interface.ts
│   │   │   │
│   │   │   └── notifications/       # Notifications Module
│   │   │       ├── notifications.module.ts
│   │   │       ├── controllers/
│   │   │       │   └── notifications.controller.ts
│   │   │       ├── services/
│   │   │       │   ├── notifications.service.ts
│   │   │       │   ├── fcm.service.ts
│   │   │       │   └── push-notification.service.ts
│   │   │       ├── dto/
│   │   │       │   ├── register-token.dto.ts
│   │   │       │   └── send-notification.dto.ts
│   │   │       └── entities/
│   │   │           └── notification-token.entity.ts
│   │   │
│   │   ├── infrastructure/         # Infrastructure Layer
│   │   │   ├── cache/
│   │   │   │   ├── cache.module.ts
│   │   │   │   ├── redis.service.ts
│   │   │   │   └── cache-key.builder.ts
│   │   │   ├── queue/
│   │   │   │   ├── queue.module.ts
│   │   │   │   ├── bull.service.ts
│   │   │   │   └── queue.constants.ts
│   │   │   ├── websocket/
│   │   │   │   ├── websocket.module.ts
│   │   │   │   ├── websocket.gateway.ts
│   │   │   │   └── websocket.adapter.ts
│   │   │   ├── logger/
│   │   │   │   ├── logger.module.ts
│   │   │   │   ├── winston.service.ts
│   │   │   │   └── log-formats.ts
│   │   │   └── health/
│   │   │       ├── health.module.ts
│   │   │       └── health.controller.ts
│   │   │
│   │   └── shared/                 # Shared Domain Objects
│   │       ├── entities/
│   │       │   └── base.entity.ts
│   │       ├── repositories/
│   │       │   └── base.repository.ts
│   │       ├── value-objects/
│   │       │   ├── email.vo.ts
│   │       │   ├── uuid.vo.ts
│   │       │   └── money.vo.ts
│   │       └── utils/
│   │           ├── crypto.util.ts
│   │           └── date.util.ts
│   │
│   ├── views/                      # Admin Panel Views (Server-Side)
│   │   ├── layouts/
│   │   │   ├── admin.hbs
│   │   │   └── auth.hbs
│   │   ├── partials/
│   │   │   ├── header.hbs
│   │   │   ├── sidebar.hbs
│   │   │   ├── footer.hbs
│   │   │   ├── pagination.hbs
│   │   │   └── alerts.hbs
│   │   ├── admin/
│   │   │   ├── login.hbs
│   │   │   └── dashboard.hbs
│   │   ├── articles/
│   │   │   ├── list.hbs
│   │   │   ├── edit.hbs
│   │   │   └── create.hbs
│   │   ├── users/
│   │   │   ├── list.hbs
│   │   │   └── detail.hbs
│   │   ├── llm/
│   │   │   └── stats.hbs
│   │   └── sources/
│   │       └── list.hbs
│   │
│   ├── public/                     # Static Assets for Admin
│   │   ├── css/
│   │   │   ├── admin.css
│   │   │   └── tailwind.output.css
│   │   ├── js/
│   │   │   ├── admin.js
│   │   │   ├── charts.js
│   │   │   └── datatables.js
│   │   └── images/
│   │       ├── logo.png
│   │       └── favicon.ico
│   │
│   ├── scripts/                    # Utility Scripts
│   │   ├── seed.ts
│   │   ├── migrate.ts
│   │   └── test-ollama.ts
│   │
│   ├── test/                       # Test Files
│   │   ├── unit/
│   │   ├── integration/
│   │   └── e2e/
│   │
│   ├── .env.example
│   ├── nest-cli.json
│   ├── tsconfig.json
│   ├── tsconfig.build.json
│   └── package.json
│
├── docker/                         # Docker Configuration
│   ├── docker-compose.yml
│   ├── docker-compose.dev.yml
│   ├── docker-compose.prod.yml
│   ├── ollama/
│   │   ├── Dockerfile
│   │   ├── models.txt
│   │   └── init.sh
│   ├── backend/
│   │   └── Dockerfile
│   ├── postgres/
│   │   └── init.sql
│   └── nginx/
│       ├── nginx.conf
│       └── ssl/
│
├── docs/                          # Documentation
│   ├── API.md
│   ├── ARCHITECTURE.md
│   ├── DEPLOYMENT.md
│   ├── CONTRIBUTING.md
│   └── diagrams/
│
├── .github/                       # GitHub Configuration
│   └── workflows/
│       ├── ci.yml
│       ├── deploy.yml
│       └── test.yml
│
├── README.md
├── .gitignore
└── LICENSE
```

---

## 4. Technical Specifications

### 4.1 Backend - NestJS (Single Package.json)

```json
{
  "name": "stockbrief-backend",
  "version": "1.0.0",
  "description": "StockBrief NestJS Backend with Integrated Admin Panel",
  "scripts": {
    "build": "nest build",
    "start": "nest start",
    "start:dev": "nest start --watch",
    "start:prod": "node dist/main",
    "test": "jest",
    "seed": "ts-node scripts/seed.ts",
    "tailwind:build": "tailwindcss -i ./src/styles/admin.css -o ./public/css/admin.css --watch"
  },
  "dependencies": {
    "@nestjs/common": "^10.4.8",
    "@nestjs/core": "^10.4.8",
    "@nestjs/platform-express": "^10.4.8",
    "@nestjs/typeorm": "^10.0.2",
    "@nestjs/config": "^3.3.0",
    "@nestjs/jwt": "^10.2.0",
    "@nestjs/passport": "^10.0.3",
    "@nestjs/swagger": "^7.4.2",
    "@nestjs/throttler": "^6.2.1",
    "@nestjs/websockets": "^10.4.8",
    "@nestjs/bull": "^10.2.1",
    "@nestjs/cache-manager": "^2.3.0",
    "@nestjs/schedule": "^4.1.1",
    "@nestjs/serve-static": "^4.0.2",

    "typeorm": "^0.3.20",
    "pg": "^8.13.1",
    "redis": "^4.7.0",
    "bull": "^4.16.3",

    "passport": "^0.7.0",
    "passport-jwt": "^4.0.1",
    "passport-local": "^1.0.0",
    "bcrypt": "^5.1.1",

    "express-session": "^1.18.1",
    "connect-redis": "^7.1.1",
    "hbs": "^4.2.0",
    "express-handlebars": "^8.0.1",

    "class-validator": "^0.14.1",
    "class-transformer": "^0.5.1",
    "helmet": "^8.0.0",
    "compression": "^1.7.5",
    "cookie-parser": "^1.4.7",

    "axios": "^1.7.7",
    "winston": "^3.15.0",
    "moment": "^2.30.1",
    "chart.js": "^4.4.6"
  },
  "devDependencies": {
    "@nestjs/cli": "^10.4.8",
    "@nestjs/testing": "^10.4.8",
    "@types/node": "^22.10.0",
    "typescript": "^5.7.2",
    "tailwindcss": "^3.4.15",
    "jest": "^29.7.0",
    "prettier": "^3.4.1"
  }
}
```

### 4.2 Flutter Dependencies (Latest Versions)

```yaml
dependencies:
  flutter:
    sdk: flutter

  flutter_riverpod: ^2.6.1
  dio: ^5.7.0
  go_router: ^14.6.0
  hive_flutter: ^1.1.0
  flutter_secure_storage: ^9.2.2
  cached_network_image: ^3.4.1
  webview_flutter: ^4.10.0
  firebase_core: ^3.8.0
  firebase_messaging: ^15.1.5
  freezed_annotation: ^2.4.4
  json_annotation: ^4.9.0
  get_it: ^8.0.0
  injectable: ^2.5.0
```

---

## 5. NestJS Backend Implementation

### 5.1 Main Application Setup

```typescript
// main.ts
import { NestFactory } from "@nestjs/core";
import { NestExpressApplication } from "@nestjs/platform-express";
import { AppModule } from "./app.module";
import { join } from "path";
import * as hbs from "hbs";
import * as session from "express-session";
import * as cookieParser from "cookie-parser";
import { ValidationPipe } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";
import * as compression from "compression";
import * as helmet from "helmet";

async function bootstrap() {
  const app = await NestFactory.create<NestExpressApplication>(AppModule);
  const configService = app.get(ConfigService);

  // Security and middleware
  app.use(cookieParser());
  app.use(compression());
  app.use(
    helmet({
      contentSecurityPolicy: false, // Allow inline scripts for admin panel
    })
  );

  // Session for admin panel
  app.use(
    session({
      secret: configService.get("SESSION_SECRET"),
      resave: false,
      saveUninitialized: false,
      cookie: {
        maxAge: 1000 * 60 * 60 * 24, // 24 hours
        httpOnly: true,
      },
    })
  );

  // View engine for admin panel
  app.setBaseViewsDir(join(__dirname, "..", "views"));
  app.setViewEngine("hbs");
  hbs.registerPartials(join(__dirname, "..", "views", "partials"));

  // Handlebars helpers
  hbs.registerHelper("eq", (a, b) => a === b);
  hbs.registerHelper("json", (context) => JSON.stringify(context));
  hbs.registerHelper("formatDate", (date) =>
    new Date(date).toLocaleDateString()
  );

  // Static files for admin panel
  app.useStaticAssets(join(__dirname, "..", "public"), {
    prefix: "/admin/assets/",
  });

  // Global validation
  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,
      transform: true,
    })
  );

  // Different prefixes for API and Admin
  app.setGlobalPrefix("api", {
    exclude: ["admin", "admin/*"],
  });

  // CORS for mobile app
  app.enableCors({
    origin: configService.get("CORS_ORIGINS").split(","),
    credentials: true,
  });

  const port = configService.get("PORT", 3000);
  await app.listen(port);

  console.log(`🚀 Server running on http://localhost:${port}`);
  console.log(`📱 API: http://localhost:${port}/api`);
  console.log(`🎛️ Admin: http://localhost:${port}/admin`);
  console.log(`📚 API Docs: http://localhost:${port}/api/docs`);
}
bootstrap();
```

### 5.2 App Module Configuration

```typescript
// app.module.ts
import { Module } from "@nestjs/common";
import { ConfigModule } from "@nestjs/config";
import { TypeOrmModule } from "@nestjs/typeorm";
import { BullModule } from "@nestjs/bull";
import { ScheduleModule } from "@nestjs/schedule";
import { ThrottlerModule } from "@nestjs/throttler";

// Feature Modules
import { AuthModule } from "./modules/auth/auth.module";
import { AdminModule } from "./modules/admin/admin.module";
import { UsersModule } from "./modules/users/users.module";
import { ArticlesModule } from "./modules/articles/articles.module";
import { LlmModule } from "./modules/llm/llm.module";
import { NewsModule } from "./modules/news/news.module";

@Module({
  imports: [
    // Configuration
    ConfigModule.forRoot({
      isGlobal: true,
      envFilePath: `.env.${process.env.NODE_ENV || "development"}`,
    }),

    // Database
    TypeOrmModule.forRoot({
      type: "postgres",
      host: process.env.DB_HOST,
      port: +process.env.DB_PORT,
      username: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME,
      autoLoadEntities: true,
      synchronize: process.env.NODE_ENV !== "production",
    }),

    // Queue
    BullModule.forRoot({
      redis: {
        host: process.env.REDIS_HOST,
        port: +process.env.REDIS_PORT,
      },
    }),

    // Scheduler
    ScheduleModule.forRoot(),

    // Rate limiting
    ThrottlerModule.forRoot({
      ttl: 60,
      limit: 100,
    }),

    // Feature modules
    AuthModule,
    AdminModule,
    UsersModule,
    ArticlesModule,
    LlmModule,
    NewsModule,
  ],
})
export class AppModule {}
```

---

## 6. Admin Panel Integration

### 6.1 Admin Module Structure

```typescript
// modules/admin/admin.module.ts
import { Module } from "@nestjs/common";
import { AdminAuthController } from "./controllers/admin-auth.controller";
import { AdminDashboardController } from "./controllers/admin-dashboard.controller";
import { AdminArticlesController } from "./controllers/admin-articles.controller";
import { AdminUsersController } from "./controllers/admin-users.controller";
import { AdminAuthService } from "./services/admin-auth.service";
import { AdminDashboardService } from "./services/admin-dashboard.service";
import { AdminAuthGuard } from "./guards/admin-auth.guard";
import { ArticlesModule } from "../articles/articles.module";
import { UsersModule } from "../users/users.module";
import { LlmModule } from "../llm/llm.module";

@Module({
  imports: [
    // Import other modules to access their services
    ArticlesModule,
    UsersModule,
    LlmModule,
  ],
  controllers: [
    AdminAuthController,
    AdminDashboardController,
    AdminArticlesController,
    AdminUsersController,
  ],
  providers: [AdminAuthService, AdminDashboardService, AdminAuthGuard],
})
export class AdminModule {}
```

### 6.2 Admin Controller with Server-Side Rendering

```typescript
// modules/admin/controllers/admin-dashboard.controller.ts
import {
  Controller,
  Get,
  Post,
  Render,
  UseGuards,
  Session,
  Query,
  Param,
  Body,
  Redirect,
} from "@nestjs/common";
import { AdminAuthGuard } from "../guards/admin-auth.guard";
import { AdminDashboardService } from "../services/admin-dashboard.service";

@Controller("admin")
export class AdminDashboardController {
  constructor(private readonly dashboardService: AdminDashboardService) {}

  @Get("login")
  @Render("admin/login")
  loginPage(@Session() session: any) {
    if (session.admin) {
      return { redirect: "/admin" };
    }
    return { title: "Admin Login" };
  }

  @Post("login")
  async login(
    @Body() loginDto: { email: string; password: string },
    @Session() session: any
  ) {
    const admin = await this.dashboardService.validateAdmin(
      loginDto.email,
      loginDto.password
    );

    if (admin) {
      session.admin = admin;
      return { redirect: "/admin" };
    }

    return {
      redirect: "/admin/login?error=Invalid credentials",
    };
  }

  @Get()
  @UseGuards(AdminAuthGuard)
  @Render("admin/dashboard")
  async dashboard(@Session() session: any) {
    const stats = await this.dashboardService.getDashboardStats();

    return {
      title: "Dashboard",
      admin: session.admin,
      stats,
      layout: "layouts/admin",
    };
  }

  @Get("articles")
  @UseGuards(AdminAuthGuard)
  @Render("admin/articles/list")
  async articles(@Query("page") page = 1, @Query("status") status?: string) {
    const { articles, total } = await this.dashboardService.getArticles({
      page,
      limit: 20,
      status,
    });

    return {
      title: "Articles Management",
      articles,
      pagination: {
        current: page,
        total: Math.ceil(total / 20),
      },
      layout: "layouts/admin",
    };
  }

  @Post("articles/:id/approve")
  @UseGuards(AdminAuthGuard)
  @Redirect("/admin/articles")
  async approveArticle(@Param("id") id: string) {
    await this.dashboardService.approveArticle(id);
    return { message: "Article approved successfully" };
  }

  @Post("articles/bulk-action")
  @UseGuards(AdminAuthGuard)
  @Redirect("/admin/articles")
  async bulkAction(@Body() body: { action: string; ids: string[] }) {
    await this.dashboardService.bulkAction(body.action, body.ids);
    return { message: `${body.ids.length} articles updated` };
  }

  @Get("logout")
  @Redirect("/admin/login")
  logout(@Session() session: any) {
    session.destroy();
    return {};
  }
}
```

### 6.3 Admin View Templates

```handlebars
{{! views/layouts/admin.hbs }}

<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{{title}} - StockBrief Admin</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="/admin/assets/css/admin.css" rel="stylesheet" />
  </head>
  <body class="bg-gray-50">
    <div class="flex h-screen">
      <!-- Sidebar -->
      <aside class="w-64 bg-gray-900 text-white">
        <div class="p-4">
          <h1 class="text-2xl font-bold">StockBrief Admin</h1>
        </div>
        <nav class="mt-8">
          <a
            href="/admin"
            class="block px-4 py-2 hover:bg-gray-800"
          >Dashboard</a>
          <a
            href="/admin/articles"
            class="block px-4 py-2 hover:bg-gray-800"
          >Articles</a>
          <a
            href="/admin/users"
            class="block px-4 py-2 hover:bg-gray-800"
          >Users</a>
          <a href="/admin/llm" class="block px-4 py-2 hover:bg-gray-800">LLM
            Stats</a>
          <a
            href="/admin/sources"
            class="block px-4 py-2 hover:bg-gray-800"
          >News Sources</a>
          <a
            href="/admin/logout"
            class="block px-4 py-2 hover:bg-gray-800 mt-8"
          >Logout</a>
        </nav>
      </aside>

      <!-- Main Content -->
      <main class="flex-1 overflow-y-auto">
        <div class="p-8">
          {{{body}}}
        </div>
      </main>
    </div>

    <script src="/admin/assets/js/admin.js"></script>
  </body>
</html>
```

```handlebars
{{!-- views/admin/dashboard.hbs --}}
<div class="mb-8">
    <h2 class="text-3xl font-bold">Dashboard</h2>
    <p class="text-gray-600">Welcome back, {{admin.name}}</p>
</div>

<div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-gray-500 text-sm">Total Users</h3>
        <p class="text-3xl font-bold">{{stats.totalUsers}}</p>
        <p class="text-green-500 text-sm">+{{stats.newUsersToday}} today</p>
    </div>

    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-gray-500 text-sm">Articles</h3>
        <p class="text-3xl font-bold">{{stats.totalArticles}}</p>
        <p class="text-yellow-500 text-sm">{{stats.pendingArticles}} pending</p>
    </div>

    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-gray-500 text-sm">LLM Summaries</h3>
        <p class="text-3xl font-bold">{{stats.summariesToday}}</p>
        <p class="text-blue-500 text-sm">{{stats.avgProcessingTime}}s avg</p>
    </div>

    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-gray-500 text-sm">System Health</h3>
        <p class="text-3xl font-bold text-green-500">Healthy</p>
        <p class="text-gray-500 text-sm">All systems operational</p>
    </div>
</div>

<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-xl font-bold mb-4">Recent Articles</h3>
        <div class="space-y-2">
            {{#each stats.recentArticles}}
            <div class="border-b pb-2">
                <p class="font-medium">{{title}}</p>
                <p class="text-sm text-gray-600">{{source}} - {{relativeTime publishedAt}}</p>
                <span class="text-xs px-2 py-1 rounded
                    {{#if (eq status 'published')}}bg-green-100 text-green-800{{/if}}
                    {{#if (eq status 'pending')}}bg-yellow-100 text-yellow-800{{/if}}">
                    {{status}}
                </span>
            </div>
            {{/each}}
        </div>
    </div>

    <div class="bg-white p-6 rounded-lg shadow">
        <h3 class="text-xl font-bold mb-4">Queue Status</h3>
        <canvas id="queueChart"></canvas>
    </div>
</div>

<script>
// Initialize charts
document.addEventListener('DOMContentLoaded', function() {
    const ctx = document.getElementById('queueChart').getContext('2d');
    new Chart(ctx, {
        type: 'doughnut',
        data: {
            labels: ['Processing', 'Waiting', 'Completed', 'Failed'],
            datasets: [{
                data: [
                    {{stats.queue.processing}},
                    {{stats.queue.waiting}},
                    {{stats.queue.completed}},
                    {{stats.queue.failed}}
                ],
                backgroundColor: [
                    '#3B82F6',
                    '#F59E0B',
                    '#10B981',
                    '#EF4444'
                ]
            }]
        }
    });
});
</script>
```

---

## 7. Development Phases

### 7.1 Development Timeline (4 Weeks)

**Week 1: Flutter Static UI**

- Day 1-2: Project setup, core screens
- Day 3-4: Onboarding flow (index/stock selection)
- Day 5-6: Feed UI, article cards
- Day 7: Navigation, polish

**Week 2: NestJS Backend Setup**

- Day 8: Docker, Ollama setup
- Day 9-10: NestJS project, database
- Day 11-12: API endpoints
- Day 13-14: Admin panel views

**Week 3: Integration**

- Day 15-16: Flutter-API connection
- Day 17-18: Feed implementation
- Day 19-20: Admin panel functionality
- Day 21: Testing

**Week 4: Launch**

- Day 22-23: LLM integration, testing
- Day 24-25: Performance optimization
- Day 26: Production deployment
- Day 27-28: Store submission

---

## 8. API Specifications

### 8.1 API Routes (JWT Auth)

```typescript
// Mobile App APIs
GET    /api/auth/login
POST   /api/auth/register
POST   /api/auth/refresh
GET    /api/indices
POST   /api/users/indices
GET    /api/stocks?indices=NSE,BSE
POST   /api/users/stocks
GET    /api/feed?page=1&limit=20
GET    /api/articles/:id
POST   /api/articles/:id/save
DELETE /api/articles/:id/save
```

### 8.2 Admin Routes (Session Auth)

```typescript
// Admin Panel Routes (Server-Side Rendered)
GET    /admin/login
POST   /admin/login
GET    /admin                    // Dashboard
GET    /admin/articles           // Articles list
GET    /admin/articles/:id/edit  // Edit form
POST   /admin/articles/:id/approve
POST   /admin/articles/bulk-action
GET    /admin/users              // Users list
GET    /admin/llm/stats          // LLM statistics
GET    /admin/logout
```

---

## 9. Success Metrics

### 9.1 Technical KPIs

- **Single Codebase:** 1 backend project (not 2)
- **API Response:** <200ms (p95)
- **Admin Page Load:** <1 second
- **LLM Processing:** <3 seconds
- **Code Coverage:** >80%
- **Zero External API Costs**

### 9.2 Architecture Benefits

- **Simplified Deployment:** Single backend to deploy
- **Shared Logic:** Services reused between API and admin
- **Consistent Data:** Same database queries
- **Easier Maintenance:** One codebase to update
- **Lower Costs:** Single server instance

---

## 10. Quick Reference

### 10.1 Commands

```bash
# Development
cd backend
npm run start:dev     # Starts both API and Admin
npm run tailwind:build # Build admin CSS

# Access
http://localhost:3000/api     # Mobile API
http://localhost:3000/admin   # Admin Panel

# Docker
docker-compose up -d  # Start all services

# Testing
npm run test         # Unit tests
npm run test:e2e     # E2E tests
```

### 10.2 Environment Variables

```env
# .env
NODE_ENV=development
PORT=3000

# Database
DB_HOST=localhost
DB_PORT=5432
DB_USER=admin
DB_PASSWORD=password
DB_NAME=stockbrief

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# Session
SESSION_SECRET=your-secret-key

# JWT
JWT_SECRET=your-jwt-secret
JWT_EXPIRES_IN=7d

# Ollama
OLLAMA_URL=http://localhost:11434

# CORS
CORS_ORIGINS=http://localhost:3000,http://localhost:8080
```

### 10.3 Key Architecture Points

✅ **What We Have:**

- Single NestJS backend serving both API and admin
- Server-side rendered admin panel (no React)
- Session auth for admin, JWT for API
- Shared business logic between both
- Clean architecture with proper separation

❌ **What We Don't Have:**

- Separate React admin project
- Multiple package.json files
- Complex build processes
- Duplicate business logic
- Additional deployment complexity

---

## 📌 Summary

**Product:** StockBrief

**Architecture:** Single NestJS backend with integrated admin panel

**Tech Stack:**

- Mobile: Flutter 3.27+ (Clean Architecture)
- Backend: NestJS 10.4+ (API + Admin)
- Admin UI: Handlebars/EJS (Server-Side Rendered)
- Database: PostgreSQL 16
- Cache: Redis 7.4
- LLM: Ollama (Local)

**Cost:** ~$40-80/month (no AI API costs)

**Timeline:** 4 weeks to MVP

**Key Benefit:** Simplified architecture with single backend serving everything

---

**END OF PRD v5.0**

_Version 5.0 - Unified Backend Edition_

_Last Updated: November 2, 2025_

🚀 **Build StockBrief with a single, unified backend!**
