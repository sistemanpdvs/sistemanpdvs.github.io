---
title: Contatos
icon: fas fa-mail
order: 5
---

## Contatos

<!-- START HERE -->
   <link rel="stylesheet" href="https://unpkg.com/purecss@1.0.0/build/pure-min.css">
   <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.4.0/css/font-awesome.min.css">
   <!-- Style The Contact Form How Ever You Prefer -->
   <link rel="stylesheet" href="/css/form.css">

  <form class="gform pure-form pure-form-stacked" method="POST" data-email="example@email.net"
  action="https://script.google.com/macros/s/AKfycbzMSuwbT1AC48fP7BxzUWf1DPALk5V7WjfD3jjnZ98/exec">
    <!-- change the form action to your script url -->
    <div class="form-elements">
      <fieldset class="pure-group">
        <label for="name">Nome: </label>
        <input id="name" name="name" required placeholder="Digite seu nome" />
      </fieldset>

      <fieldset class="pure-group">
        <label for="email"><em>Seu</em> Endereço de email:</label>
        <input id="email" name="email" type="email" value=""
        required placeholder="seu.nome@email.com"/>
        <span class="email-invalid" style="display:none">
          Digite um email válido</span>
      </fieldset>

      <fieldset class="pure-group">
        <label for="assunto">Assunto: </label>
        <input id="assunto" name="assunto" required placeholder="Assunto"></textarea>
      </fieldset>

      <fieldset class="pure-group">
        <label for="message">Mensagem: </label>
        <textarea id="message" name="message" rows="7"
        required placeholder="Digite sua mensagem..."></textarea>
      </fieldset>

      <button class="button-success pure-button button-xlarge">
        <i class="fa fa-paper-plane"></i>&nbsp;Enviar</button>
    </div>

    <!-- Customise the Thankyou Message People See when they submit the form: -->
    <div class="thankyou_message" style="display:none;">
      <center><h2>Opa! Sua mensagem acabou de chegar em minha caixa de entrada!
        Responderei assim que possível. Obrigado brother!</h2></center>
                  <center><img src="/images/send.gif" width="600" height="370"></center>
    </div>

  </form>

  <!-- Submit the Form to Google Using "AJAX" -->
  <script data-cfasync="false" type="text/javascript"
  src="/script/form-submission-handler.js"></script>
<!-- END -->
