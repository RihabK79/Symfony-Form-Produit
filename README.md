# Symfony-Form-Produit

Ce projet montre un exemple simple d’un formulaire **Produit** en Symfony, utilisant :
- FormType
- Controller
- Twig
- Bootstrap 5

## Fichier : ProduitType.php
Ce fichier définit le formulaire Symfony permettant de choisir :
- la quantité
- la couleur
- et d’ajouter le produit au panier


```
<?php
declare(strict_types=1);

namespace App\Form\Type;

use Symfony\Component\Form\AbstractType;
use Symfony\Component\Form\Extension\Core\Type\ChoiceType;
use Symfony\Component\Form\Extension\Core\Type\IntegerType;
use Symfony\Component\Form\Extension\Core\Type\SubmitType;
use Symfony\Component\Form\FormBuilderInterface;

class ProduitType extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options): void
    {
        $builder
            ->add('quantite', IntegerType::class, [
                'label' => 'Quantité',
                'required' => true,
                'data' => 1,
                'attr' => [
                    'class' => 'form-control mb-3',
                    'min' => 1,
                    'style' => 'max-width:70px;'
                ],
            ])

            ->add('color', ChoiceType::class, [
                'label' => 'Couleur',
                'choices' => [
                    'Matte Black' => 'matte_black',
                    'Pearl White' => 'pearl_white',
                    'Silver' => 'silver',
                ],
                'attr' => [
                    'class' => 'form-select mb-3',
                    'style' => 'max-width:150px;'
                ],
            ])

            ->add('submit', SubmitType::class, [
                'label' => 'Add to cart',
                'attr' => [
                    'class' => 'btn btn-primary'
                ],
            ]);
    }
}

```

## Fichier : ProduitController.php
Ce contrôleur :
  - crée le formulaire ProduitType
  - l’envoie à la vue Twig pour affichage

```
<?php

namespace App\Controller;

use App\Form\Type\ProduitType;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;
use Symfony\Component\OptionsResolver\OptionsResolver;

final class ProductController extends AbstractController
{
    #[Route('/product', name: 'app_product')]
    public function index(): Response
    {
        $produitForm = $this->createForm(ProduitType::class);
        return $this->render('prroduit/index.html.twig',
        ['produit' => $produitForm,]);
    }
    public function configureOptions(OptionsResolver $resolver): void
    {
    $resolver->setDefaults([
        'csrf_protection' => true,
        'csrf_field_name' => '__produit_token__',
        'csrf_token_id' => 'Produit',

    ]);
    }
}
```


## Fichier : index.html.twig
Ce fichier Twig affiche :
  - les informations du produit
  - l’image
  - le formulaire Symfony stylisé avec Bootstrap

```
{% extends 'base.html.twig' %}

{% block body %}
<div class="container my-5">
    <div class="row">
        <div class="col-md-6">
            <img
                src="https://images.pexels.com/photos/90946/pexels-photo-90946.jpeg?auto=compress&cs=tinysrgb&w=800"
                class="img-fluid rounded"
                alt="Produit">
        </div>

        <div class="col-md-6">
            <h1 class="mb-3">Premium Wireless Headphones</h1>
            <p class="text-muted fs-4 mb-3">$129.99</p>

            <p class="mb-4">
                Experience superior sound quality with our premium wireless headphones.
                Features active noise cancellation, 30-hour battery life, and premium comfort padding.
            </p>

            <ul class="list-unstyled mb-4">
                <li><strong>Brand:</strong> AudioTech</li>
                <li><strong>Connectivity:</strong> Bluetooth 5.0</li>
                <li><strong>Battery Life:</strong> 30 hours</li>
            </ul>

            {{ form_start(produitForm) }}
                <div>
                    {{ form_row(produitForm.quantite) }}
                </div>

                <div>
                    {{ form_row(produitForm.color) }}
                </div>

                <div>
                    {{ form_row(produitForm.submit) }}
                </div>
            {{ form_end(produitForm) }}

        </div>
    </div>
</div>
{% endblock %}
```

