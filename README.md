# 🚀 Prompt Completo para Recriar NotifyPro

Cole este prompt em outro app (Claude, ChatGPT, etc) para recriar o NotifyPro com exatidão total.

---

## PROMPT PARA COLAR

```
Você é um desenvolvedor sênior especialista em React Native + Expo SDK 54.

Preciso que você crie um aplicativo mobile chamado NotifyPro do ZERO com as seguintes especificações EXATAS:

====================
1️⃣ STACK TÉCNICO
====================
- React Native 0.81 + Expo SDK 54
- TypeScript 5.9
- React 19
- Expo Router 6 (file-based routing)
- NativeWind 4 (Tailwind CSS)
- expo-notifications 0.32.15
- expo-image-picker 17.0.10
- AsyncStorage para persistência
- Vitest para testes

====================
2️⃣ ESTRUTURA DO PROJETO
====================
Criar com scaffold mobile-app:
- app/(tabs)/index.tsx → Home screen principal
- app/_layout.tsx → Root layout com providers
- components/ → Componentes reutilizáveis
- hooks/ → Custom hooks
- lib/ → Utilitários
- assets/images/ → Ícones e imagens
- theme.config.js → Configuração de cores

====================
3️⃣ DESIGN E CORES (OBRIGATÓRIO)
====================
Dark Mode com:
- background: #151718 (grafite escuro)
- foreground: #ECEDEE (branco)
- primary: #00FF7F (neon green)
- surface: #1e2022
- muted: #9BA1A6
- border: #334155
- success: #4ADE80
- error: #F87171

Orientação: Portrait (9:16)
Uso: One-handed (botões no bottom)

====================
4️⃣ TELAS E FUNCIONALIDADES
====================

### Home Screen (app/(tabs)/index.tsx)
- Header com logo "NotifyPro" + descrição
- Seletor de ícones pré-definidos (WhatsApp, Kiwify, Hotmart, Genérico) em carrossel horizontal
- Picker de ícone customizado (importar de galeria/câmera)
- Campo de entrada: "Título Principal" (ex: "Venda Aprovada! ✅")
- Campo de entrada: "Descrição" (ex: "Parabéns, você fez uma venda!")
- Slider: Quantidade de notificações (1-50)
- Slider: Intervalo de tempo em segundos (1-60)
- Botão principal "📤 Agendar Notificações" com:
  - Animação scale (0.95) ao pressionar
  - Feedback tátil (Haptics)
  - Estado loading com spinner
  - Disabled quando sem permissão
- Status visual de permissão (✅ ativa / ❌ inativa)

====================
5️⃣ SISTEMA DE NOTIFICAÇÕES (CRÍTICO)
====================

### Permissões
- requestPermissionsAsync() ao iniciar app
- Validar status real (granted/denied)
- Logs claros: "[NotifyPro] Permissão: granted"

### Agendamento com DELAY ACUMULADO
- Cada notificação em tempo MAIOR que a anterior
- Exemplo: intervalo=5s, quantidade=3
  → Notif 1: 5s
  → Notif 2: 10s
  → Notif 3: 15s
- Usar Date trigger (não seconds)
- Cancelar anteriores antes de agendar novas

### Android
- Criar canal: setNotificationChannelAsync()
- ID: "default-channel"
- Nome: "Notificações"
- Permissão: POST_NOTIFICATIONS
- Compatível com Android 13+

### iOS
- sound: "default"
- badge: 1
- Suporte a attachments (ícone)
- Background modes: "remote-notification", "fetch"

### Handlers
- setNotificationHandler() no entry point
- Listener foreground: mostrar notificação mesmo com app aberto
- Listener response: log quando usuário toca
- Logs em cada etapa: "[NotifyPro] 📬 Notificação recebida"

====================
6️⃣ PERSISTÊNCIA COM ASYNCSTORAGE
====================

Interface AppSettings:
```typescript
{
  title: string;              // Título principal
  description: string;        // Descrição
  customIconUri?: string;     // URI do ícone customizado
  quantity: number;           // 1-50
  interval: number;           // 1-60 segundos
  selectedIcon: string;       // "whatsapp" | "kiwify" | "hotmart" | "generic"
}
```

Default:
```typescript
{
  title: "Venda Aprovada! ✅",
  description: "Parabéns, você fez uma venda!",
  quantity: 5,
  interval: 10,
  selectedIcon: "generic"
}
```

Salvar automaticamente ao mudar cada campo.

====================
7️⃣ COMPONENTES NECESSÁRIOS
====================

### ScheduleButton
- Animação scale (0.95) + opacity
- Feedback tátil ao pressionar
- Spinner visual durante agendamento
- Disabled quando inválido
- Log: "[NotifyPro] Button pressed"

### IconCarousel
- Carrossel horizontal de 4 ícones
- Cada ícone com label (WhatsApp, Kiwify, Hotmart, Genérico)
- Seleção visual (border highlight)
- onSelectIcon callback

### IconPicker
- Botões: "📁 Galeria" e "📷 Câmera"
- Preview do ícone selecionado
- Botão para remover ícone
- Permissões: MediaLibrary + Camera

### CustomSlider
- Slider customizado com Animated API
- Label + valor atual
- Min/Max + step
- Feedback visual ao arrastar

### ScreenContainer
- SafeArea wrapper
- Background color extends behind status bar
- Edges: ["top", "left", "right"]

====================
8️⃣ HANDLERS E LÓGICA
====================

### handleScheduleNotifications()
1. Log: "[NotifyPro] handleScheduleNotifications called"
2. Validar permissão
3. Haptic feedback ao iniciar
4. Chamar scheduleNotifications() com:
   - quantity
   - interval
   - title
   - description
   - customIconUri
5. Mostrar Alert com sucesso/erro
6. Haptic success/error feedback

### scheduleNotifications()
1. Log: "[NotifyPro] 📅 scheduleNotifications chamado com: {...}"
2. Validar parâmetros (quantity 1-50, interval 1-60)
3. Cancelar notificações anteriores
4. Loop para cada notificação:
   - delaySeconds = interval * (i + 1)  ← DELAY ACUMULADO
   - triggerDate = now + delaySeconds
   - scheduleNotificationAsync() com Date trigger
   - Log: "[NotifyPro] ✅ Notificação X/Y agendada - ID: xxx"
5. Retornar true se sucesso

====================
9️⃣ TESTES (VITEST)
====================

### tests/integration.test.ts
- Testar cálculo de delays acumulativos
- Testar geração de conteúdo de notificações
- Testar validação de ranges (quantity 1-50, interval 1-60)
- Testar seleção de ícones (4 opções)
- Testar formatação de título e descrição

### hooks/__tests__/use-app-settings.test.ts
- Testar default settings
- Testar persistência com AsyncStorage
- Testar atualização de cada campo
- Testar validação de ranges

### hooks/__tests__/use-notifications.test.ts
- Testar cálculo de delays acumulativos com Date
- Testar agendamento com múltiplas notificações
- Testar cancelamento de anteriores
- Testar tratamento de erros

Mínimo: 20 testes passando

====================
🔟 CONFIGURAÇÃO (app.config.ts)
====================

```typescript
const env = {
  appName: "NotifyPro",
  appSlug: "notifypro",
  logoUrl: "",  // URL S3 do ícone customizado
  scheme: "manus...",
  iosBundleId: "space.manus.notifypro.t...",
  androidPackage: "space.manus.notifypro.t...",
};

// Permissões obrigatórias:
android: {
  permissions: [
    "POST_NOTIFICATIONS",
    "RECEIVE_BOOT_COMPLETED",
    "WAKE_LOCK",
    "SCHEDULE_EXACT_ALARM"
  ]
}

ios: {
  infoPlist: {
    UIBackgroundModes: ["remote-notification", "fetch"],
    NSUserNotificationUsageDescription: "...",
  }
}

// Plugin expo-notifications:
plugins: [
  ["expo-notifications", {
    microphonePermission: "..."
  }]
]
```

====================
1️⃣1️⃣ LOGS OBRIGATÓRIOS
====================

Cada ação deve logar com padrão:
```
[NotifyPro] 📅 Ação: detalhes
[NotifyPro] ✅ Sucesso: resultado
[NotifyPro] ❌ Erro: mensagem
```

Exemplos:
- "[NotifyPro] Button pressed"
- "[NotifyPro] 📅 scheduleNotifications chamado com: {...}"
- "[NotifyPro] 📬 Notificação recebida em foreground"
- "[NotifyPro] ✅ 5 notificações agendadas com DELAY ACUMULADO!"

====================
1️⃣2️⃣ ENTREGA FINAL
====================

Entregar:
✅ Código completo e funcional
✅ Todos os componentes
✅ Hooks com persistência
✅ Sistema de notificações robusto
✅ Testes passando (mínimo 20)
✅ Sem erros TypeScript
✅ Dark mode funcionando
✅ Delay acumulado testado

Não incluir:
❌ Backend/servidor
❌ Autenticação de usuário
❌ Cloud sync
❌ Pagamentos
```

---

## COMO USAR ESTE PROMPT

1. **Copie todo o texto acima** (do "PROMPT PARA COLAR" até o final)
2. **Cole em outro app** (Claude, ChatGPT, Copilot, etc)
3. **Aguarde a resposta** - o app será criado do zero
4. **Valide** se ficou igual ao NotifyPro original

---

## CHECKLIST DE VALIDAÇÃO

Após recriar, verifique:

- [ ] App abre sem erros
- [ ] Dark mode com cores corretas (neon green)
- [ ] Seletor de ícones funciona
- [ ] IconPicker importa imagem
- [ ] Campos de título e descrição salvam
- [ ] Sliders funcionam (1-50, 1-60)
- [ ] Botão "Agendar" dispara notificações
- [ ] Notificações chegam com DELAY (5s, 10s, 15s)
- [ ] Permissões solicitadas corretamente
- [ ] Testes passam (pnpm test)
- [ ] Sem erros TypeScript (pnpm check)

---

## DÚVIDAS FREQUENTES

**P: E se o outro app não suportar Expo?**
R: Use este prompt como base e adapte para React Native puro ou Flutter.

**P: Posso usar este prompt para produção?**
R: Sim, mas você precisará fazer EAS Build para compilar para iOS/Android.

**P: Como adicionar mais funcionalidades depois?**
R: Use o mesmo padrão de componentes, hooks e testes.

---

Boa sorte! 🚀
