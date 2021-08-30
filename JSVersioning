<?php
define('DS', DIRECTORY_SEPARATOR);

class JSVersioning
{
	public $destDir;
	public $cache = [];
	public $version = 0;
	public $pattern = '/(.js\?v=[0-9]\w+\')|(.js\')/i';
	public $replaceString = '.js?v={version}\'';

	public function __construct($destDir)
	{
		$this->destDir = $destDir;
	}

	private function searhFile($dir, $result = 'object', $ext = ['.js','.css'])
	{
		$Dir = array_diff(scandir($dir), ['.','..']);
		$cache = [];
		foreach ($Dir as $index => $_dir) {
			$path = dirname($dir) . DS . basename($dir) . DS . $_dir . DS;
			if (is_dir($path))
			{
				$cache[$_dir] = $this->searhFile($path, 'array');
			}
			else if (preg_match('/(.js)/i', $path))
			{
				$this->cache[] = rtrim($path, '/');
			}
		}

		return ($result === 'object') ? $this : $cache;
	}

	private function setVersion($path)
	{
		$stream = file_get_contents($path);

		if (preg_match($this->pattern, $stream))
		{
			$replaceWith = str_replace('{version}', $this->version,$this->replaceString);
			return preg_replace($this->pattern, $replaceWith, $stream);
		}
	}

	public function generate()
	{
		$this->searhFile($this->destDir);

		foreach ($this->cache as $dir => $pathJS) {
			$process = $this->setVersion($pathJS);
			if (!is_null($process))
			{
				echo "Upgrade version for $pathJS \n";
				file_put_contents($pathJS, $process);
			}
		}
	}
}

// Create Instance
$Jsv = new JSVersioning(__DIR__ . '/assets/js/components/');
$Jsv->version = 400;
$Jsv->generate();

