# [PHP] Generate PDF Document

> PHP, DomPDF, Imagick

#### Include package
`$ composer require dompdf/dompdf`

`$ sudo apt-get install php-imagick`

```php
function index()
{
    $dompdf = new Dompdf();
    $svg = file_get_contents(public_path("img/svg_image.svg"));
    $svg = "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"no\"?>".$svg;
    
    $html_id = "id1";

    $svg = preg_replace( '/<path id="('.$html_id.')"/', '<path style="fill:#f26e21; opacity: .6;"', $svg );

    $svg = preg_replace( '/<path id="([^"]+)"/', '<path id="$1" style="display:none;"', $svg );

    $im = new Imagick();
    $im->setBackgroundColor(new ImagickPixel('transparent'));
    $im->readImageBlob($svg);
    $im->setImageFormat("png32");
    $context = view('pdf', ["img" => base64_encode($im)]);

    $dompdf->loadHtml($context->render());

    $opt = new Options();
    $opt->setIsHtml5ParserEnabled(true);
    $opt->setFontDir(public_path("fonts"));

    $dompdf->setOptions( $opt );
    $dompdf->setPaper('A4', 'landscape');
    $dompdf->render();
    $dompdf->stream();
}
```
