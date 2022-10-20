Tiempo de Sesión dinámico
========================

Por ahora, está planteado para tener una tabla en la base de datos con ese valor.

Este middleware hay que añadirlo en el Kernel.php justo antes del middleware “StartSession” para que se cierre la sesión del usuario si disminuye ese tiempo y se asigne antes.

```
<?php

namespace App\Http\Middleware;

use Closure;

/**
 * Class SessionLifeTime
 *
 * Establece el tiempo de sesión dinámicamente según el valor configurado
 * por el administrador.
 */
class SessionLifeTime
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        ## Obtenemos el valor de la configuración de sesión
        $webSetting = \App\WebSetting::where('field', 'session')->first();

        if (!$webSetting || !$webSetting->value) {
            return $next($request);
        }

        ## Tiempo de la sesión en minutos.
        $lifetime = $webSetting->value ?? 60;

        ## Establecemos el tiempo de la sesión en la configuración.
        config(['session.lifetime' => $lifetime]);


        return $next($request);
    }
}
```