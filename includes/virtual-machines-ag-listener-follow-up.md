Después de crear el agente de escucha del grupo de disponibilidad, puede que sea necesario ajustar los parámetros de clúster **RegisterAllProvidersIP** y **HostRecordTTL** para el recurso del agente de escucha.  Estos parámetros pueden reducir el tiempo de reconexión tras una conmutación por error que puede evitar los tiempos de expiración de la conexión. Para obtener más información sobre estos parámetros, así como un código de ejemplo, consulte el tema [Crear o configurar un agente de escucha del grupo de disponibilidad](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover).



<!--HONumber=Nov16_HO3-->


