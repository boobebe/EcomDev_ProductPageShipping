EcomDev_ProductPageShipping
===========================

Estimate Shipping on the Product Page



Fonte alterações:
http://comunidademagento.com.br/portal/calculo-de-frete-na-pagina-do-produto/





ara isso, siga os seguintes passos:

1º – Baixe a extensão no seguinte link

https://github.com/IvanChepurnyi/EcomDev_ProductPageShipping/archive/dev.zip

Descompacte o arquivo baixado e instale ela manualmente na sua loja!

Obs: Não aconselho baixar a extensão no Magento Connect, já que ela está bem desatualizada.

2º – Abra o arquivo Estimate.php (app -> code -> community -> EcomDev -> ProductPageShipping -> Model) e substitua o seguinte código:

1
2
3
4
public function getProduct()
{
return $this->_product;
}
Por esse:

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
public function getProduct()
 {
 //Verify if the product is configurable, since configurable products doesn’t have weight to estimate
 if($this->_product->isConfigurable()){
 //For convenience, creates a new variable just for our product
 $configurableProduct = $this->_product;
 //Load an array with all the associated products
 $associated_products = $configurableProduct->loadByAttribute('sku', $configurableProduct->getSku())->getTypeInstance()->getUsedProducts();
 //Run foreach just once to get the first of the associated products
 foreach($associated_products as $assoc){
 $this->_product = $assoc;
 break;
 }
 //Return the product
 return $this->_product;
 }
 return $this->_product;
 }
Essa alteração solucionará o problema em relação aos produtos configuráveis!

3º – Em Sistema -> Configuração -> CATÁLOGO -> Product Page Shipping, habilite a extensão, escolha aonde ela será exibida (coluna da esquerda, direita etc) e que campos irão aparecer (cep, cidade, estado etc).

magento-imagem-frete-02

Caso você queira deixar a “caixa” para o cálculo dentro das informações do produto (como mostrado na imagem abaixo), selecione em Display In a opção Layout Personalizado e cole o seguinte código no seu view.phtml (app -> design -> frontend -> default -> SeuTema -> template -> catalog -> product):

1
<?php echo $this->getLayout()->getBlock('shipping.estimate.form')->toHtml()?>
Nesse caso, coloquei após o seguinte código:

1
<?php echo $this->getChildHtml('extrahint') ?>
magento-imagem-frete-04

—— Postagem Editada – 19/06/2013 ——

4º (Bônus!) – Desde que eu utilizava a versão 1.6.2.0 do Magento, essa extensão apresenta um bug ao tentar comentar algum produto, exibindo o erro:

Fatal error: Call to a member function toHtml() on a non-object in ../view.phtml on line XX

Se você tiver esse problema, vá até o arquivo config.xml (app->code->community->EcomDev->ProductPageShipping->etc) e procure a seguinte linha:

1
<controller_actions>
Logo após ela, insira o seguinte:

1
2
<review_product_list />
<review_product_view />
—— Postagem Editada – 08/07/2013 ——

5º (Bônus!) – Retirar a obrigatoriedade do campo CEP.

Para que o preenchimento do campo CEP não seja obrigatório, é necessário fazer o seguinte:

- No arquivo form.phtml (app->design->frontend->base->default->template->ecomdev->productpageshipping->estimate), procure pelo seguinte trecho:

1
<input class="input-text validate-postcode<?php if ($this->isFieldRequired('postcode')):?> required-entry<?php endif;?>" type="text" id="estimate_postcode" name="estimate[postcode]" value="<?php echo $this->htmlEscape($this->getFieldValue('postcode')) ?>" />
E substitua ele todo por esse:

1
<input class="input-text validate-postcode" type="text" id="estimate_postcode" name="estimate[postcode]" value="<?php echo $this->htmlEscape($this->getFieldValue('postcode')) ?>" />
Então é isso, qualquer dúvida, sugestão ou correção, deixe um comentário!

Fonte:  http://www.ecomdev.org/2010/06/22/estimate-the-product-shipping-rate-on-its-page.html#comment-4015

